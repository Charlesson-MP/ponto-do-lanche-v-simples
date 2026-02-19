<script setup lang="ts">
import { ref } from 'vue'
import type { Product } from '../types/product'
import { X } from 'lucide-vue-next'

const WHATSAPP_NUMBER = '5577991153244'

const props = defineProps<{
  isOpen: boolean
  product: Product | null
}>()

const emit = defineEmits<{
  close: []
}>()

const quantity = ref(1)
const address = ref('')
const name = ref('')
const paymentMethod = ref('pix')
const changeFor = ref('')

function close() {
  emit('close')
  // Reset fields after closing
  setTimeout(() => {
    quantity.value = 1
    address.value = ''
    name.value = ''
    paymentMethod.value = 'pix'
    changeFor.value = ''
  }, 300)
}

function sendOrder() {
  if (!props.product) return

  const total = props.product.price * quantity.value

  let paymentInfo = `Pagamento: *${paymentMethod.value.toUpperCase()}*`
  if (paymentMethod.value === 'dinheiro' && changeFor.value) {
    paymentInfo += `\nTroco para: *R$ ${changeFor.value}*`
  }

  const text = encodeURIComponent(
    `Olá! Gostaria de fazer um pedido:\n\n` +
    `*${props.product.name}*\n` +
    `Quantidade: *${quantity.value}*\n` +
    `Endereço: *${address.value.trim() || 'A retirar'}*\n` +
    (name.value ? `Nome: *${name.value.trim()}*\n` : '') +
    `${paymentInfo}\n` +
    `Total Estimado: *R$ ${total.toFixed(2)}*\n\n` +
    `Aguardo confirmação!`
  )

  window.open(`https://wa.me/${WHATSAPP_NUMBER}?text=${text}`, '_blank')
  close()
}
</script>

<template>
  <div v-if="isOpen" class="fixed inset-0 z-50 flex items-center justify-center p-4">
    <!-- Overlay -->
    <div class="absolute inset-0 bg-black/50 backdrop-blur-sm" @click="close"></div>

    <!-- Modal Content -->
    <div
      class="bg-white rounded-2xl shadow-xl w-full max-w-md relative z-10 overflow-hidden animate-fade-in-up max-h-[90vh] overflow-y-auto">
      <!-- Header -->
      <div class="bg-red-600 p-4 flex items-center justify-between text-white sticky top-0 z-20">
        <h3 class="font-bold text-lg">Detalhes do Pedido</h3>
        <button @click="close" class="hover:bg-red-700 p-1 rounded transition">
          <X class="w-6 h-6" />
        </button>
      </div>

      <!-- Body -->
      <div class="p-6 space-y-4" v-if="product">
        <div class="flex items-center gap-4 border-b border-gray-100 pb-4">
          <img :src="product.image" :alt="product.name" class="w-16 h-16 rounded-lg object-cover">
          <div>
            <h4 class="font-bold text-gray-800">{{ product.name }}</h4>
            <p class="text-red-600 font-semibold">R$ {{ product.price.toFixed(2) }}</p>
          </div>
        </div>

        <!-- Quantidade -->
        <div>
          <label class="block text-sm font-medium text-gray-700 mb-1">Quantidade</label>
          <div class="flex items-center gap-3">
            <button @click="quantity > 1 ? quantity-- : null"
              class="w-8 h-8 rounded-full bg-gray-100 hover:bg-gray-200 flex items-center justify-center font-bold text-gray-600 transition"
              :disabled="quantity <= 1" :class="{ 'opacity-50 cursor-not-allowed': quantity <= 1 }">-</button>
            <span class="text-lg font-semibold w-8 text-center">{{ quantity }}</span>
            <button @click="quantity++"
              class="w-8 h-8 rounded-full bg-red-100 hover:bg-red-200 text-red-600 flex items-center justify-center font-bold transition">+</button>
          </div>
        </div>

        <!-- Nome -->
        <div>
          <label class="block text-sm font-medium text-gray-700 mb-1">Seu Nome (Opcional)</label>
          <input v-model="name" type="text" placeholder="Ex: João Silva"
            class="w-full px-3 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-red-500 focus:border-red-500 outline-none transition">
        </div>

        <!-- Endereço -->
        <div>
          <label class="block text-sm font-medium text-gray-700 mb-1">Endereço de Entrega</label>
          <textarea v-model="address" rows="2" placeholder="Rua, Número, Bairro..."
            class="w-full px-3 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-red-500 focus:border-red-500 outline-none transition resize-none"></textarea>
        </div>

        <!-- Pagamento -->
        <div>
          <label class="block text-sm font-medium text-gray-700 mb-1">Forma de Pagamento</label>
          <select v-model="paymentMethod"
            class="w-full px-3 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-red-500 focus:border-red-500 outline-none transition bg-white">
            <option value="pix">PIX</option>
            <option value="cartao">Cartão (Maquininha)</option>
            <option value="dinheiro">Dinheiro</option>
          </select>
        </div>

        <!-- Troco (Condicional) -->
        <div v-if="paymentMethod === 'dinheiro'" class="animate-fade-in">
          <label class="block text-sm font-medium text-gray-700 mb-1">Troco para quanto? (Opcional)</label>
          <input v-model="changeFor" type="number" placeholder="Ex: 50"
            class="w-full px-3 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-red-500 focus:border-red-500 outline-none transition">
        </div>

        <!-- Total -->
        <div class="flex items-center justify-between pt-2 border-t border-gray-100">
          <span class="text-gray-600">Total Estimado:</span>
          <span class="text-xl font-bold text-red-600">R$ {{ (product.price * quantity).toFixed(2) }}</span>
        </div>
      </div>

      <!-- Footer -->
      <div class="p-4 bg-gray-50 flex gap-3 sticky bottom-0 z-20">
        <button @click="close"
          class="flex-1 px-4 py-2 border border-gray-300 text-gray-700 rounded-lg hover:bg-gray-100 font-semibold transition">
          Cancelar
        </button>
        <button @click="sendOrder"
          class="flex-1 px-4 py-2 bg-green-600 text-white rounded-lg hover:bg-green-700 font-semibold shadow-md hover:shadow-lg transition">
          Enviar Pedido
        </button>
      </div>
    </div>
  </div>
</template>

<style scoped>
.animate-fade-in-up {
  animation: fadeInUp 0.3s ease-out;
}

.animate-fade-in {
  animation: fadeIn 0.3s ease-out;
}

@keyframes fadeInUp {
  from {
    opacity: 0;
    transform: translateY(20px);
  }

  to {
    opacity: 1;
    transform: translateY(0);
  }
}

@keyframes fadeIn {
  from {
    opacity: 0;
  }

  to {
    opacity: 1;
  }
}
</style>

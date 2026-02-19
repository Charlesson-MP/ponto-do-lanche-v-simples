<script setup lang="ts">
import type { Product } from '../types/product'
import { MessageCircle } from 'lucide-vue-next'

const WHATSAPP_NUMBER = '5577991153244'

const props = defineProps<{
  product: Product
}>()

const emit = defineEmits<{
  ordered: [productName: string]
}>()

function sendWhatsApp() {
  const text = encodeURIComponent(
    `Olá! Gostaria de pedir:\n\n` +
    `🍔 *${props.product.name}*\n` +
    `💰 R$ ${props.product.price.toFixed(2)}\n\n` +
    `Aguardo confirmação, obrigado!`
  )
  window.open(`https://wa.me/${WHATSAPP_NUMBER}?text=${text}`, '_blank')
  emit('ordered', props.product.name)
}
</script>

<template>
  <div class="bg-white rounded-2xl shadow-md overflow-hidden
           hover:shadow-lg transition duration-300 hover:-translate-y-1 hover:scale-[1.02]">
    <!-- Imagem -->
    <img :src="product.image" :alt="product.name" class="w-full h-48 object-cover" loading="lazy" />

    <!-- Conteúdo -->
    <div class="p-4 flex flex-col gap-2">
      <h3 class="text-xl font-semibold text-brown">
        {{ product.name }}
      </h3>

      <p class="text-gray-500 text-sm">
        {{ product.category }}
      </p>

      <p class="text-gray-400 text-xs leading-relaxed">
        {{ product.description }}
      </p>

      <div class="flex items-center justify-between mt-2">
        <span class="text-lg font-bold text-red-600">
          R$ {{ product.price.toFixed(2) }}
        </span>

        <button @click="sendWhatsApp"
          class="bg-green-600 text-white px-4 py-2 rounded-lg hover:bg-green-700 transition flex items-center gap-2 text-sm font-semibold">
          <MessageCircle class="w-4 h-4" />
          Pedir
        </button>
      </div>
    </div>
  </div>
</template>

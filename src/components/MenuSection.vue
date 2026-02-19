<script setup lang="ts">
import { computed } from 'vue'
import type { Product } from '../types/product'
import ProductCard from './ProductCard.vue'

const props = defineProps<{
  products: Product[]
}>()

const emit = defineEmits<{
  'select-product': [product: Product]
}>()

// Agrupa por categoria mantendo a ordem de aparição
const groupedProducts = computed(() => {
  const groups: { category: string; items: Product[] }[] = []
  const seen = new Set<string>()

  for (const product of props.products) {
    if (!seen.has(product.category)) {
      seen.add(product.category)
      groups.push({ category: product.category, items: [] })
    }
    groups.find(g => g.category === product.category)!.items.push(product)
  }

  return groups
})

// Emoji por categoria
const categoryEmoji: Record<string, string> = {
  'Hambúrguer': '🍔',
  'Bebidas': '🥤',
  'Porções': '🍟',
  'Acompanhamentos': '🥗',
}
</script>

<template>
  <section id="cardapio" class="bg-bg-hero px-6 md:px-10 pb-12">
    <h2 class="text-4xl text-brown font-bold py-8 text-center">Cardápio</h2>

    <div v-for="group in groupedProducts" :key="group.category" class="mb-10">
      <h3 class="text-2xl text-brown font-bold mb-5 flex items-center gap-2">
        <span>{{ categoryEmoji[group.category] || '🍽️' }}</span>
        {{ group.category }}
      </h3>

      <div class="grid gap-6 sm:grid-cols-2 lg:grid-cols-3">
        <ProductCard v-for="product in group.items" :key="product.id" :product="product"
          @select-product="(p) => emit('select-product', p)" />
      </div>
    </div>
  </section>
</template>

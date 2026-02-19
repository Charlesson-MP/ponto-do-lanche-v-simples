<script setup lang="ts">
import { ref } from 'vue'
import Header from './components/Header.vue'
import HeroSection from './components/HeroSection.vue'
import MenuSection from './components/MenuSection.vue'
import Footer from './components/Footer.vue'
import OrderModal from './components/OrderModal.vue'
import { products } from './data/products'
import type { Product } from './types/product'

const isModalOpen = ref(false)
const selectedProduct = ref<Product | null>(null)

function handleSelectProduct(product: Product) {
  selectedProduct.value = product
  isModalOpen.value = true
}

function handleCloseModal() {
  isModalOpen.value = false
}
</script>

<template>
  <Header />
  <main>
    <HeroSection />
    <MenuSection :products="products" @select-product="handleSelectProduct" />
  </main>
  <Footer />

  <OrderModal :isOpen="isModalOpen" :product="selectedProduct" @close="handleCloseModal" />
</template>

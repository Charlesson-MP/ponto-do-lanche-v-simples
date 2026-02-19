<script setup lang="ts">
import { ref } from 'vue'
import Header from './components/Header.vue'
import HeroSection from './components/HeroSection.vue'
import MenuSection from './components/MenuSection.vue'
import Footer from './components/Footer.vue'
import Toast from './components/Toast.vue'
import { products } from './data/products'

const toastVisible = ref(false)
const toastMessage = ref('')

function handleOrdered(productName: string) {
  toastMessage.value = `Pedido de "${productName}" enviado via WhatsApp! 🎉`
  toastVisible.value = true
}

function hideToast() {
  toastVisible.value = false
}
</script>

<template>
  <Header />
  <main>
    <HeroSection />
    <MenuSection :products="products" @ordered="handleOrdered" />
  </main>
  <Footer />
  <Toast :message="toastMessage" :visible="toastVisible" @hide="hideToast" />
</template>

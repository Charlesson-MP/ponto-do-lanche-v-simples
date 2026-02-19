<script setup lang="ts">
import { ref, nextTick, onMounted, onBeforeUnmount } from 'vue'
import { Menu, X } from 'lucide-vue-next'
import logo from '../assets/images/logo-ponto-do-lanche.webp'

const isMenuOpen = ref(false)
const menuButton = ref<HTMLElement | null>(null)
const menuDrawer = ref<HTMLElement | null>(null)

function focusAfterRender(getEl: () => HTMLElement | null) {
  nextTick(() => {
    requestAnimationFrame(() => {
      getEl()?.focus()
    })
  })
}

function openMenu() {
  isMenuOpen.value = true
  focusAfterRender(() => menuDrawer.value)
}

function closeMenu() {
  isMenuOpen.value = false
  focusAfterRender(() => menuButton.value)
}

function scrollTo(id: string) {
  closeMenu()
  nextTick(() => {
    document.getElementById(id)?.scrollIntoView({ behavior: 'smooth' })
  })
}

function handleKeydown(e: KeyboardEvent) {
  if (!isMenuOpen.value) return
  if (e.key === 'Escape') closeMenu()
}

onMounted(() => document.addEventListener('keydown', handleKeydown))
onBeforeUnmount(() => document.removeEventListener('keydown', handleKeydown))
</script>

<template>
  <header class="w-full bg-primary px-2 py-2 flex items-center justify-between shadow-header lg:px-10 relative z-50">
    <div class="flex items-center">
      <button ref="menuButton" class="p-2 lg:hidden" aria-label="Abrir menu" :aria-expanded="isMenuOpen"
        @click="openMenu">
        <Menu class="w-8 h-8 p-1 text-white" :stroke-width="3" />
      </button>
      <img :src="logo" alt="Logo Ponto do Lanche" class="h-20">
    </div>

    <!-- Backdrop mobile -->
    <div v-if="isMenuOpen" class="fixed inset-0 bg-black/50 z-40 lg:hidden" @click="closeMenu"></div>

    <!-- Nav drawer / desktop nav -->
    <nav ref="menuDrawer" role="dialog" aria-modal="true" aria-label="Menu de navegação" tabindex="-1"
      :aria-hidden="!isMenuOpen"
      class="bg-white lg:bg-transparent w-64 lg:w-auto fixed lg:static top-0 left-0 h-screen lg:h-auto flex flex-col lg:flex-row z-50 lg:z-auto shadow-xl lg:shadow-none transition-transform duration-300 ease-in-out lg:translate-x-0 outline-none"
      :class="isMenuOpen ? 'translate-x-0' : '-translate-x-full invisible lg:visible'">

      <div class="flex justify-between items-center p-5 border-b lg:hidden">
        <p class="text-lg font-bold text-gray-800">Menu</p>
        <button aria-label="Fechar menu" @click="closeMenu">
          <X class="w-6 h-6 text-gray-600 hover:text-gray-900" :stroke-width="2.5" />
        </button>
      </div>

      <ul class="list-none flex flex-col lg:flex-row gap-6 lg:gap-8 items-center p-5 lg:p-0">
        <li>
          <button @click="scrollTo('hero')"
            class="text-lg font-bold text-gray-800 lg:text-white lg:hover:bg-primary-dark lg:px-5 lg:py-2 lg:rounded-full transition-colors">
            Home
          </button>
        </li>
        <li>
          <button @click="scrollTo('cardapio')"
            class="text-lg font-bold text-gray-800 lg:text-white lg:hover:bg-primary-dark lg:px-5 lg:py-2 lg:rounded-full transition-colors">
            Cardápio
          </button>
        </li>
        <li>
          <button @click="scrollTo('contato')"
            class="text-lg font-bold text-gray-800 lg:text-white lg:hover:bg-primary-dark lg:px-5 lg:py-2 lg:rounded-full transition-colors">
            Contato
          </button>
        </li>
      </ul>
    </nav>
  </header>
</template>

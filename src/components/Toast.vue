<script setup lang="ts">
import { ref, watch } from 'vue'
import { CheckCircle } from 'lucide-vue-next'

const props = defineProps<{
  message: string
  visible: boolean
}>()

const emit = defineEmits<{
  hide: []
}>()

let timer: ReturnType<typeof setTimeout> | null = null

watch(() => props.visible, (val) => {
  if (val) {
    if (timer) clearTimeout(timer)
    timer = setTimeout(() => emit('hide'), 3000)
  }
})
</script>

<template>
  <Transition name="toast">
    <div v-if="visible"
      class="fixed bottom-6 left-1/2 -translate-x-1/2 z-[100] bg-green-600 text-white px-6 py-3 rounded-xl shadow-2xl flex items-center gap-3 font-semibold text-sm">
      <CheckCircle class="w-5 h-5 flex-shrink-0" />
      <span>{{ message }}</span>
    </div>
  </Transition>
</template>

<style scoped>
.toast-enter-active {
  animation: toast-in 0.35s ease-out;
}
.toast-leave-active {
  animation: toast-out 0.25s ease-in forwards;
}

@keyframes toast-in {
  from {
    opacity: 0;
    transform: translateX(-50%) translateY(20px);
  }
  to {
    opacity: 1;
    transform: translateX(-50%) translateY(0);
  }
}

@keyframes toast-out {
  from {
    opacity: 1;
    transform: translateX(-50%) translateY(0);
  }
  to {
    opacity: 0;
    transform: translateX(-50%) translateY(20px);
  }
}
</style>

<script setup lang="ts">
import { ref, onMounted, onUnmounted } from 'vue'
import { useFloating, offset, flip, shift, autoUpdate } from '@floating-ui/vue'
import LanguageSelector from '@/components/LanguageSelector.vue'
import { useI18n } from 'vue-i18n'

defineProps<{
  isDarkMode: boolean
  isDarkModePreferenceSetBySystem: boolean
}>()

const emit = defineEmits<{
  (e: 'toggle-dark-mode'): void
  (e: 'close'): void
}>()

const { t } = useI18n()
const isOpen = ref(false)
const reference = ref<HTMLElement | null>(null)
const floating = ref<HTMLElement | null>(null)

const { floatingStyles } = useFloating(reference, floating, {
  placement: 'bottom-end',
  middleware: [offset(5), flip(), shift()],
  whileElementsMounted: autoUpdate
})

const toggleMenu = () => {
  isOpen.value = !isOpen.value
}

const closeMenu = () => {
  isOpen.value = false
  emit('close')
}

const handleClickOutside = (event: MouseEvent) => {
  if (
    isOpen.value &&
    floating.value &&
    !floating.value.contains(event.target as Node) &&
    reference.value &&
    !reference.value.contains(event.target as Node)
  ) {
    closeMenu()
  }
}

onMounted(() => {
  document.addEventListener('click', handleClickOutside)
})

onUnmounted(() => {
  document.removeEventListener('click', handleClickOutside)
})
</script>

<template>
  <div class="transition-opacity duration-300">
    <!-- Hamburger menu button -->
    <button
      ref="reference"
      class="flex size-9 items-center justify-center rounded-md border border-zinc-300 bg-zinc-100 text-zinc-800 outline-none hover:bg-zinc-200 focus-visible:ring-1 focus-visible:ring-zinc-700 dark:border-zinc-700 dark:bg-zinc-800 dark:text-zinc-200 dark:hover:bg-zinc-700 dark:focus-visible:ring-zinc-200"
      @click="toggleMenu"
      :aria-label="t('Menu')"
      aria-haspopup="true"
      :aria-expanded="isOpen"
    >
      <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24">
        <path fill="currentColor" d="M3 18h18v-2H3zm0-5h18v-2H3zm0-5h18V6H3z" />
      </svg>
    </button>

    <!-- Dropdown menu -->
    <div
      v-if="isOpen"
      ref="floating"
      :style="floatingStyles"
      class="z-50 w-64 rounded-md border border-zinc-300 bg-white p-4 shadow-lg dark:border-zinc-700 dark:bg-zinc-800"
    >
      <div class="flex flex-col gap-4">
        <!-- App title -->
        <div class="flex items-center">
          <h1 class="text-xl text-gray-700 dark:text-gray-100">MiniQR</h1>
        </div>

        <!-- GitHub link -->

        <!-- Dark mode toggle -->
        <button
          class="flex items-center gap-2 rounded-md px-2 py-1.5 text-zinc-800 hover:bg-zinc-100 dark:text-zinc-200 dark:hover:bg-zinc-700"
          @click="emit('toggle-dark-mode')"
          :aria-label="t('Toggle dark mode')"
        >
          <span v-if="isDarkModePreferenceSetBySystem">
            <svg xmlns="http://www.w3.org/2000/svg" width="20" height="20" viewBox="0 0 24 24">
              <g fill="currentColor">
                <path d="M12 16a4 4 0 0 0 0-8z" />
                <path
                  fill-rule="evenodd"
                  d="M12 2C6.477 2 2 6.477 2 12s4.477 10 10 10s10-4.477 10-10S17.523 2 12 2m0 2v4a4 4 0 1 0 0 8v4a8 8 0 1 0 0-16"
                  clip-rule="evenodd"
                />
              </g>
            </svg>
          </span>
          <span v-else-if="isDarkMode">
            <svg
              xmlns="http://www.w3.org/2000/svg"
              width="20"
              height="20"
              viewBox="0 0 24 24"
              fill="none"
              stroke="currentColor"
              stroke-width="2"
            >
              <path
                stroke-linecap="round"
                stroke-linejoin="round"
                d="M12 3v1m0 16v1m9-9h-1M4 12H3m15.364 6.364l-.707-.707M6.343 6.343l-.707-.707m12.728 0l-.707.707M6.343 17.657l-.707.707M16 12a4 4 0 11-8 0 4 4 0 018 0z"
              />
            </svg>
          </span>
          <span v-else>
            <svg
              xmlns="http://www.w3.org/2000/svg"
              width="20"
              height="20"
              viewBox="0 0 24 24"
              fill="currentColor"
              stroke-width="2"
            >
              <path
                stroke-linecap="round"
                stroke-linejoin="round"
                d="M20.354 15.354A9 9 0 018.646 3.646 9.003 9.003 0 0012 21a9.003 9.003 0 008.354-5.646z"
              />
            </svg>
          </span>
          <span>{{ t('Toggle dark mode') }}</span>
        </button>

        <!-- Language selector -->
        <div class="px-2 py-1.5">
          <LanguageSelector />
        </div>
      </div>
    </div>
  </div>
</template>

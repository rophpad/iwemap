<script setup lang="ts">
import LanguageSelector from './LanguageSelector.vue'
</script>

<template>
  <header class="flex items-center justify-between p-4 bg-white relative">
    <div class="flex gap-4">
      <span class="text-xs p-4 rounded-xl bg-gray-100 hover:bg-blue-100 cursor-pointer">Emplacements des bibliothèques</span>
      <span class="text-xs p-4 rounded-xl bg-gray-100 hover:bg-blue-100 cursor-pointer">Comment prêter un livre ?</span>
    </div>
    <img
      src="/src/assets/images/logo_BE.png"
      alt=""
      class="w-12 absolute top-1/2 right-1/2 -translate-y-1/2 -translate-x-1/2"
    />
    <LanguageSelector />
  </header>
</template>

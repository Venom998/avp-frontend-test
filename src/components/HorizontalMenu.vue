<script setup>
import IconLogo from './icons/IconLogo.vue'
import { RouterLink } from 'vue-router'
</script>

<template>
  <aside class="flex w-full bg-indigo-600 text-white shadow-xl">
    <div class="flex items-center gap-2 px-5 py-3">
      <IconLogo class="w-6 h-6" />
      <RouterLink to="/" class="text-lg font-semibold">Locomotive Frontend</RouterLink>
    </div>
  </aside>
</template>

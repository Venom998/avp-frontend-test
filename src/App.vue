<script setup>
import { RouterView } from 'vue-router'
import HorizontalMenu from './components/HorizontalMenu.vue'
</script>

<template>
  <div class="flex w-full min-h-screen mx-auto flex-col">
    <HorizontalMenu />

    <RouterView />
  </div>
</template>

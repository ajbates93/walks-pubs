
<script setup lang="ts">
import HeaderBar from './components/HeaderBar.vue'
</script>

<template>
  <header-bar />
  <router-view v-slot="{ Component }">
    <transition 
        class="transition-all"
        :enter-active-class="'fade-in ease-out'" :leave-active-class="'fade-out ease-in'"
        mode="out-in"
      >
        <component :is="Component" />
      </transition>
  </router-view>
</template>

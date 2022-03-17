
<script setup lang="ts">
import HeaderBar from './components/HeaderBar.vue'
</script>

<template>
  <header-bar />
  <router-view v-slot="{ Component }">
    <transition 
        class="animate__animated animate__faster"
        :enter-active-class="'animate__fadeIn'" :leave-active-class="'animate__fadeOut'"
        mode="out-in"
      >
        <component :is="Component" />
      </transition>
  </router-view>
</template>

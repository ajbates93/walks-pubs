<template>
  <router-view v-slot="{ Component }">
    <transition 
        class="transition-all"
        :enter-active-class="'fade ease-out'" :leave-active-class="'fade ease-in'"
        mode="out-in"
      >
        <component :is="Component" />
      </transition>
  </router-view>
</template>
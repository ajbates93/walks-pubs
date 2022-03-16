<script setup lang="ts">
import { ref, onMounted } from 'vue';  

const show = ref(true)

// onMounted(() => {
//   setTimeout(() => {
//     show.value = true
//   }, 0)
// })
</script>

<template>
  <div class="flex flex-col justify-center">
    <h1 class="text-center my-10 text-6xl font-bold text-gray-700" id="title">About</h1>
    <div class="max-w-screen-xl mx-auto text-left">
      <p>Hi! I hope you find this small site helpful. I made it out of a desire to combine my two passions; walking and pubs.</p>
    </div>
    <picture class="max-w-xl mx-auto my-10 rounded-lg overflow-clip transition-opacity" :class="show ? 'opacity-1' : 'opacity-0'">
      <img src="@/assets/dog.jpg" alt="dog" />
    </picture>
  </div>
</template>
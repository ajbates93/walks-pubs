<script setup lang="ts">
import LocationInput from './components/LocationInput.vue'

</script>

<template>
  <div class="h-screen flex flex-col justify-center">
    <h1 class="text-center my-5 text-indigo-400 text-5xl font-bold">Walks &amp; Pubs</h1>
    <p class="text-center my-5 text-3xl text-gray-800 font-bold">Need inspiration for a walk? Looking for a nice, cosy pub? Look no further.</p>
    <div id="body" class="my-5 flex items-center justify-center">
      <location-input />
    </div>
  </div>
</template>
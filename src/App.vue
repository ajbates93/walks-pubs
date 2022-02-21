<script setup lang="ts">
/* eslint-disable-no-undef */
import LocationInput from './components/LocationInput.vue'
import { computed, onMounted, ref } from 'vue'
import { useGeolocation } from './useGeolocation'
import { Loader } from '@googlemaps/js-api-loader'

const GOOGLE_MAPS_API_KEY = 'AIzaSyCOY6-U5wGpWin0Cggur-In11Bhbx7mAXI'

const { coords } = useGeolocation()
const currPos = computed(() => ({
  lat: coords.value.latitude,
  lng: coords.value.longitude
}))

const loader = new Loader({ apiKey: GOOGLE_MAPS_API_KEY })
const mapDiv = ref(null)
onMounted(async () => {
  await loader.load()
  new google.maps.Map(mapDiv.value, {
    center: currPos.value,
    zoom: 7
  })
})

</script>

<template>
  <div class="h-screen flex flex-col justify-center">
    <h1 class="text-center my-5 text-indigo-400 text-5xl font-bold">Walks &amp; Pubs</h1>
    <p class="text-center my-5 text-3xl text-gray-800 font-bold">Need inspiration for a walk? Looking for a nice, cosy pub? Look no further.</p>
    <div id="body" class="my-5 text-center">
      <!-- <h4 class="text-lg">Your position</h4>
      <p>Latitude: {{ currPos.lat.toFixed(2) }}, Longitude: {{ currPos.lng.toFixed(2) }}</p> -->
      <location-input />
    </div>
    <div ref="mapDiv" style="width: 100%; height: 60vh"></div>
  </div>
</template>
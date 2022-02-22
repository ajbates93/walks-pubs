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

const getLocation = async () => {
  await loader.load()
  new google.maps.Map(mapDiv.value, {
    center: currPos.value,
    zoom: 14
  })
}

</script>

<template>
  <div class="h-screen flex flex-col justify-center">
    <h1 class="text-center my-5 text-indigo-400 text-5xl font-bold">Walks &amp; Pubs</h1>
    <p class="text-center my-5 text-3xl text-gray-800 font-bold">Need inspiration for a walk? Looking for a nice, cosy pub? Look no further.</p>
    <div id="body" class="my-5 text-center">
      <!-- <h4 class="text-lg">Your position</h4>
      <p>Latitude: {{ currPos.lat.toFixed(2) }}, Longitude: {{ currPos.lng.toFixed(2) }}</p> -->
      <location-input @getLocation="getLocation" />
    </div>
    <div class="relative bg-gray-200" ref="mapDiv" style="width: 100%; height: 60vh">
      <div class="block flex flex-col justify-center items-center text-gray-400 h-full">
        <svg xmlns="http://www.w3.org/2000/svg" class="h-16 w-16 my-2" fill="none" viewBox="0 0 24 24" stroke="currentColor">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 20l-5.447-2.724A1 1 0 013 16.382V5.618a1 1 0 011.447-.894L9 7m0 13l6-3m-6 3V7m6 10l4.553 2.276A1 1 0 0021 18.382V7.618a1 1 0 00-.553-.894L15 4m0 13V4m0 0L9 7" />
        </svg>
        <p class="text-gray-400 font-bold">Enter your location to view results...</p>
      </div>
    </div>
  </div>
</template>
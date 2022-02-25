<script setup lang="ts">
import { computed, onMounted, ref, reactive } from "vue"

interface Coordinates {
  lat: Number,
  lng: Number
}

const emits = defineEmits(['getLocation', 'getLocationFromInput'])

const getLocationFromBrowser = () => {
  emits('getLocation')
}
const getLocationFromInput = () => {
  emits('getLocationFromInput', {currPos, initAutocomplete})
}

const currPos: Coordinates = reactive<Coordinates>({
  lat: 0,
  lng: 0
})

const autocomplete = ref(null)

const initAutocomplete = () => {
  const ac = new google.maps.places.Autocomplete(
    autocomplete.value
  )
  ac.setComponentRestrictions({
    country: ['gb']
  })
  ac.addListener("place_changed", () => {
    const place = ac.getPlace()
    
    // update local geometry state
    Object.assign(currPos, {
      lat: parseFloat(place.geometry.location.lat()),
      lng: parseFloat(place.geometry.location.lng())
    })
    
    getLocationFromInput()
  })
  return ac
}

onMounted(() => {
  initAutocomplete()
})

</script>

<template>
  <div class="p-5">
    <div class="flex items-center justify-center">
      <div class="flex border-2 rounded-lg relative">
        <button class="flex bg-gray-300 hover:bg-gray-600 rounded-l-lg border-r-2 text-white items-center justify-center px-2 border-l">
          <a @click="getLocationFromBrowser">
            <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6" fill="none" viewBox="0 0 24 24" stroke="currentColor">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M17.657 16.657L13.414 20.9a1.998 1.998 0 01-2.827 0l-4.244-4.243a8 8 0 1111.314 0z" />
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15 11a3 3 0 11-6 0 3 3 0 016 0z" />
            </svg>
          </a>
        </button>
        <input type="text" ref="autocomplete" name="location" class="text-lg outline-0 px-4 py-2 w-80" placeholder="Enter your location...">
        <button class="flex bg-indigo-500 hover:bg-indigo-600 rounded-r-lg border-l-2 text-white items-center justify-center px-4 border-l">
            <svg class="w-6 h-6 text-gray-600" fill="#fff" xmlns="http://www.w3.org/2000/svg"
                viewBox="0 0 24 24">
                <path
                    d="M16.32 14.9l5.39 5.4a1 1 0 0 1-1.42 1.4l-5.38-5.38a8 8 0 1 1 1.41-1.41zM10 16a6 6 0 1 0 0-12 6 6 0 0 0 0 12z" />
            </svg>
        </button>
      </div>
    </div>
  </div>
</template>
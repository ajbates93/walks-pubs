<script setup lang="ts">
/* eslint-disable-no-undef */
import LocationInput from '../components/LocationInput.vue'
import HeaderBar from '../components/HeaderBar.vue'
import { computed, onMounted, reactive, ref } from 'vue'
import { useGeolocation } from '../useGeolocation'
import { Loader } from '@googlemaps/js-api-loader'

interface Coordinates {
  lat: Number,
  lng: Number
}

const GOOGLE_MAPS_API_KEY = 'AIzaSyCOY6-U5wGpWin0Cggur-In11Bhbx7mAXI'

const { coords } = useGeolocation()

const currPos = computed(() => ({
  lat: coords.value.latitude,
  lng: coords.value.longitude
}))

const loader = new Loader({ apiKey: GOOGLE_MAPS_API_KEY })
const mapDiv = ref(null)

const getLocation = async (position: Coordinates) => {
  // const url = `https://maps.googleapis.com/maps/api/place/nearbysearch/json?location=${position.lat}%2C1${position.lng}&radius=500&type=restaurant&keyword=pub&key=AIzaSyCOY6-U5wGpWin0Cggur-In11Bhbx7mAXI`
  
  await loader.load()
  new google.maps.Map(mapDiv.value, {
    center: position,
    zoom: 15
  })
  const map = new google.maps.Map(mapDiv.value, {
    center: position,
    zoom: 15
  })
  const infoWindow = new google.maps.InfoWindow({
    maxWidth: 400
  })
  const request = {
    location: position,
    radius: '500',
    type: ['bar']
  }
  const addMarker = (result: any) => {
    const infoWindowContent = `<b>${result.name}</b><br/><br/>` 
      + `${result.vicinity}` 
    const marker = new google.maps.Marker({
      position: result.geometry.location,
      map: map,
      title: result.name
    })
    marker.addListener('click', () => {
      infoWindow.setContent(infoWindowContent)
      infoWindow.open(map, marker)
    })
  }
  const service = new google.maps.places.PlacesService(map)
  service.nearbySearch(request, (results: any, status: any) => {
    for (const result in results) {
      addMarker(results[result])
    }
  })
  // const config: AxiosRequestConfig<any> = {
  //   method: 'get',
  //   url: url,
  //   headers: {
  //     'Access-Control-Allow-Origin': 'http://localhost:3000'
  //   }
  // }
}

</script>

<template>
  <header-bar />
  <div class="flex flex-col justify-center">
    <h1 class="text-center my-10 text-6xl font-bold text-gray-700" id="title">Wander In</h1>
    <p class="text-center text-2xl text-gray-400 font-bold">Need inspiration for a walk? Looking for a nice, cosy pub? Look no further.</p>
    <div id="body" class="my-5 text-center">
      <location-input @getLocation="getLocation(currPos)" @getLocationFromInput="position => getLocation(position.currPos)" />
    </div>
    <!-- <div class="oax-top-cont" ref="oaxDiv"></div> -->
    <div class="relative bg-gray-200" ref="mapDiv" style="width: 100%; height: 70vh; max-height: 600px;">
      <div class="block flex flex-col justify-center items-center text-gray-400 h-full">
        <svg xmlns="http://www.w3.org/2000/svg" class="h-16 w-16 my-2" fill="none" viewBox="0 0 24 24" stroke="currentColor">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 20l-5.447-2.724A1 1 0 013 16.382V5.618a1 1 0 011.447-.894L9 7m0 13l6-3m-6 3V7m6 10l4.553 2.276A1 1 0 0021 18.382V7.618a1 1 0 00-.553-.894L15 4m0 13V4m0 0L9 7" />
        </svg>
        <p class="text-gray-400 font-bold">Enter your location to view results...</p>
      </div>
    </div>
    <div class="flex justify-center py-5">
      <a target="_blank" class="px-2 py-1 text-sm font-bold bg-indigo-500 text-white rounded-lg" href="https://ko-fi.com/ajbates93">Like this project? Support me on Ko-Fi! ☕</a>
    </div>
  </div>
</template>

<style scoped>
#title {
  position: relative;
}
</style>
<script setup lang='ts'>
import LocationInput from '../components/LocationInput.vue'
import { computed, onMounted, reactive, ref } from 'vue'
import { useGeolocation } from '../useGeolocation'
import { Loader } from '@googlemaps/js-api-loader'

interface Coordinates {
  lat: Number,
  lng: Number
}

interface Route {
  id: String,
  name: String,
  description: String,
  distance: Number,
  estimatedDuration: Number,
  encodedRoute: String,
  createdAt: Date,
  mapUrl: String
}

const GOOGLE_MAPS_API_KEY = 'AIzaSyCOY6-U5wGpWin0Cggur-In11Bhbx7mAXI'

const { coords } = useGeolocation()

const data: Route = {
  id: "2939587407451212760",
  name: 'Burnsall to Grassington Loop',
  description: '',
  distance: 11433.961372232674,
  estimatedDuration: 8225,
  encodedRoute: "g{zhItm|J}@wAg@cA^kBASw@[aCwAiAa@cBcA[MkBSw@RsEbB[Tu@b@k@`Ak@n@WpAC|AL?Ch@w@lC_@~BIhAEfCKf@Jn@Xf@?xA_@f@yCtJ}AhEw@v@uCJaA?u@Lu@l@eBfCCn@e@hAgAjB?LMb@iC~BO?a@Rm@oCALmApA}@xFeCnRiAlFo@rFc@vZ_AhK{AfKUlFkAlJaBzKc@`C_A|D?f@MdBsCvI[~Aq@rBqCnHu@|@IDsGjEaA`@]hAm@tCGPn@~Dd@bHFlGCzCBdE_@hDiAdDqAoAWMaFkBmAYYa@EMMEcAw@I?[S[g@e@n@e@DGSC?m@ZIAIa@C@Uc@UoAc@Lw@f@yB~Bs@ZKL}@u@o@[_CqAmD{CA`@HdAC^w@|Av@}AB_@IeA@a@lDzCdAn@RcAYYSg@]qAc@eAUFi@ZgAGMYf@w@f@cAC_BB{ATsBz@}DRwAjAqEb@qA`A_CfA{CHg@p@eBjBkEZoAtAyEB_@^qANM~AoDX}@DqAGcBBw@G{@@o@Z}ANsBzAiHnBiH`@cEnAkErCmMrDwWZ_FBgCHyBn@iIRq@PEDMPu@PeBp@wAh@cBJB?{@E}AhCaCbBkAdB}BfAyBb@o@bBiDREV?VJZEd@Ml@u@|@u@dDxA|@`AbCjEZ`@VDZ?RMh@s@Po@ZJXjA^l@|@dE`@SN?hC_CLc@l@`@xAa@\\u@Za@bAg@pCi@f@`@NEZa@bA{@HA~CyJ|DwHjAiDtDqOb@kCPo@z@wAuCsHrEcBv@SjBRZLbBbAhA`@`CvAv@Z@R_@jBf@bA|@vAv@l@",
  createdAt: new Date("2022-03-17T17:10:53Z"),
  mapUrl: "https://d3o5xota0a1fcr.cloudfront.net/v6/maps/OQO5Z3LEXR2BPACPGGK43YA32PPAUKH2CXBTIJ62ZOHEDIBQA7KUVGFMLAPZ3G2PXMN6CZSR2G2AQVI4AQ7H4LDNSY7RJMOJSIGA===="
}

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
    zoom: 16
  })
  const infoWindow = new google.maps.InfoWindow({
    maxWidth: 400
  })
  const routeWindow = new google.maps.InfoWindow({
    maxWidth: 700
  })
  const request = {
    location: position,
    radius: '500',
    type: ['bar']
  }
  console.log(google.maps)
  const path = google.maps.geometry.encoding.decodePath(data.encodedRoute)
  const routeOptions = {
    strokeColor: '#6366f1',
    strokeOpacity: 1.0,
    strokeWeight: 3,
    map: map,
    path: path
  }
  const route = new google.maps.Polyline(routeOptions)
  const routeWindowContent = `<b class='text-xl'>${data.name}</b><br/><br/>` 
      + `<div class='text-base mb-3'><p><b>Distance</b>: ${data.distance.toFixed(2)}km</p><p><b>Created At</b>: ${data.createdAt.toLocaleDateString()}</p></div>`
      + `<img src='${data.mapUrl}' alt='map image' />`
      + `<div class='pt-5 text-center'><a class='text-white text-sm bg-orange-400 py-2 px-3' href='/'>View on Strava</a></div>`
  google.maps.event.addListener(route, 'click', (e) => {
    console.log('hi')
    routeWindow.setContent(routeWindowContent)
    routeWindow.setPosition(e.latLng)
    routeWindow.open(map)
  })
  const addMarker = (result: any) => {
    const infoWindowContent = `<b class='text-xl'>${result.name}</b><br/><br/>` 
      + `<span class='text-sm'>${result.vicinity}</span><br /><br />`
      + `⭐️ <b class='text-sm'>${result.rating} / 5</b>` 
    const marker = new google.maps.Marker({
      position: result.geometry.location,
      map: map,
      title: result.name,
      animation: google.maps.Animation.DROP
    })
    marker.addListener('click', () => {
      infoWindow.setContent(infoWindowContent)
      infoWindow.open(map, marker)
    })
  }
  const service = new google.maps.places.PlacesService(map)
  service.nearbySearch(request, (results: any, status: any) => {
    // set max amount to 20
    const nearest = results.slice(0, 20)
    // loop through each venue and add marker to map (with drop animation)
    for (let i = 0; i < nearest.length; i++) {
      const result = nearest[i]
      window.setTimeout(() => {
        addMarker(result)
      }, i * 150)
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
  <div class="flex flex-col justify-center">
    <h1 class="text-center my-10 text-6xl font-bold text-gray-700" id="title">Wander In</h1>
    <p class="text-center text-2xl text-gray-400 font-bold">Need inspiration for a walk? Somewhere to warm your boots? Look no further.</p>
    <div id="body" class="my-5 text-center">
      <location-input @getLocation="getLocation(currPos)" @getLocationFromInput="position => getLocation(position.currPos)" />
    </div>
    <!-- <div class="oax-top-cont" ref="oaxDiv"></div> -->
    <div class="relative bg-gray-200 rounded-xl max-w-screen-xl mx-auto" ref="mapDiv" style="width: 100%; height: 70vh; max-height: 600px;">
      <div class="block flex flex-col justify-center items-center text-gray-400 h-full">
        <svg xmlns="http://www.w3.org/2000/svg" class="h-16 w-16 my-2" fill="none" viewBox="0 0 24 24" stroke="currentColor">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 20l-5.447-2.724A1 1 0 013 16.382V5.618a1 1 0 011.447-.894L9 7m0 13l6-3m-6 3V7m6 10l4.553 2.276A1 1 0 0021 18.382V7.618a1 1 0 00-.553-.894L15 4m0 13V4m0 0L9 7" />
        </svg>
        <p class="text-gray-400 font-bold">Enter your location to view results...</p>
      </div>
    </div>
    <div class="flex justify-center flex-col items-center py-5">
      <a target="_blank" class="my-2 px-2 py-1 text-sm font-bold bg-indigo-500 text-white rounded-lg" href="https://ko-fi.com/ajbates93">Like this project? Support me on Ko-Fi! ☕</a>
      <span class="pt-3">© Alex Bates 2022</span>
    </div>
  </div>
</template>

<style scoped>
#title {
  position: relative;
}
</style>
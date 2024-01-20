<script setup>
import { ref, watch, onMounted } from 'vue'

const props = defineProps({
  locoPoints: Object
})

watch(props.locoPoints, () => {
  // props.locoPoints.points.forEach((el) => {
  //   coordinates.value = [...coordinates.value, [el.Longitude, el.Latitude]]
  // })
  if (props.locoPoints.points) {
    let prevPoint = []
    coordinates.value = [
      props.locoPoints.points.map((el) => {
        return [el.Longitude, el.Latitude]
      })
    ]

    temperaturesList.value = props.locoPoints.points.filter((el) => {
      return el.OutsideTemp != null
    })
  }

  console.log(temperaturesList)
  console.log(coordinates)
})

const center = ref([37.84800256, 59.13262376])
const projection = ref('EPSG:4326')
const zoom = ref(17)
const rotation = ref(0)
const strokeWidth = ref(10)
const strokeColor = ref('red')
const coordinates = ref([])
const temperaturesList = ref([])

const currentZoom = ref(zoom.value)
const radius = ref(currentZoom.value * currentZoom.value * 0.5)

function resolutionChanged(event) {
  currentZoom.value = event.target.getZoom()
  radius.value = currentZoom.value * 1.5
}
</script>

<template>
  <ol-map
    :loadTilesWhileAnimating="true"
    :loadTilesWhileInteracting="true"
    class="flex flex-grow h-screen w-full"
  >
    <ol-view
      ref="view"
      :center="center"
      :rotation="rotation"
      :zoom="zoom"
      :projection="projection"
      @change:resolution="resolutionChanged"
    />

    <ol-tile-layer>
      <ol-source-osm />
    </ol-tile-layer>

    <ol-overlay
      :position="[item.Longitude, item.Latitude]"
      v-for="item in temperaturesList"
      :key="item.Timestamp"
    >
      <div
        class="bg-white text-slate-400 p-7 border border-gray-300 rounded-md"
        :class="currentZoom < 17 ? 'hidden' : 'block'"
      >
        <span class="text-sm">{{ item.Timestamp }}</span>
        <h6 class="text-base text-gray-500">Температура: {{ item.OutsideTemp }}℃</h6>
      </div>
    </ol-overlay>

    <ol-vector-layer>
      <ol-source-vector>
        <ol-feature>
          <ol-geom-multi-line-string :coordinates="coordinates"></ol-geom-multi-line-string>
          <ol-style>
            <ol-style-stroke :color="strokeColor" :width="strokeWidth"></ol-style-stroke>
          </ol-style>
        </ol-feature>
      </ol-source-vector>
    </ol-vector-layer>
  </ol-map>
</template>

<style scoped></style>

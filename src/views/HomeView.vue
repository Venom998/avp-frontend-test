<script setup>
import OpenLayersMap from '../components/OpenLayersMap.vue'
import axios from 'axios'
import { onMounted, ref } from 'vue'

let location = {}
let temperatures = {}
let locoPoints = ref({})

onMounted(async () => {
  try {
    location = (await axios.get('https://e02b89469cbf77cd.mokky.dev/location')).data[0]
    temperatures = (await axios.get('https://e02b89469cbf77cd.mokky.dev/temperatures')).data[0]
    console.log(location)
    console.log(temperatures)
  } catch (error) {
    console.log(error)
  }

  locoPoints.value.locoNumber = parseInt(location.LocoNumber)
  locoPoints.value.locoType = location.LocoType
  locoPoints.value.points = await parseLocoData(location, temperatures)

  console.log(locoPoints.value)
})

const parseLocoData = async (location, temperatures) => {
  let points = []
  let na_points = []
  for (let i = 0; i < location.Timestamp.length; i++) {
    points.push({
      Timestamp: location.Timestamp[i],
      Longitude:
        location.Longitude[i] != 'NA'
          ? location.Longitude[i]
          : location.Longitude[i + 1] || location.Longitude[i - 1],
      Latitude:
        location.Latitude[i] != 'NA'
          ? location.Latitude[i]
          : location.Latitude[i + 1] || location.Latitude[i - 1],
      OutsideTemp: null
    })
  }

  location.Timestamp = location.Timestamp.map((el) => +new Date(el))
  for (let i = 0; i < temperatures.Timestamp.length; i++) {
    let temperatureTimes = +new Date(temperatures.Timestamp[i])
    let index = FindTemperaturePointIndex(temperatureTimes, location.Timestamp)
    points[index].OutsideTemp = temperatures.OutsideTemp[i]
  }

  return points
}
function FindTemperaturePointIndex(value, list) {
  for (let i = 1; i < list.length; i++) {
    if (list[i] - value >= 0) {
      if (list[i - 1] - value < list[i] - value) {
        return i - 1
      } else return i
    }
  }
  return list.length - 1
}
</script>

<template>
  <OpenLayersMap :loco-points="locoPoints" />
</template>

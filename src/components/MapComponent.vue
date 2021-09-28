<template>
  <div id="map-component" />
</template>

<script>
import mapboxgl from 'mapbox-gl'; // or "const mapboxgl = require('mapbox-gl');"

mapboxgl.accessToken =
  'pk.eyJ1IjoieWlxaW5nZ2dnIiwiYSI6ImNrdHhmY2hpMzBqemEybnRobWl1enBza3oifQ.dFwEcCgHZuW6IdqjLu4DnA';
// new mapboxgl.Map({
//   container: 'map-component', // container ID
//   style: 'mapbox://styles/mapbox/streets-v11', // style URL
//   center: [-74.5, 40], // starting position [lng, lat]
//   zoom: 9, // starting zoom
// });
export default {
  name: 'map-component',
  mounted() {
    var element = document.getElementById('map-component');
    console.log(element, 'map component');
    new mapboxgl.Map({
      container: 'map-component', // container ID
      style: 'mapbox://styles/mapbox/streets-v11', // style URL
      center: [-122.272781, 37.871666], // starting position [lng, lat]
      zoom: 15, // starting zoom
    });
  },
};
</script>

<style scoped>
body {
  margin: 0;
  padding: 0;
}
#map-component {
  width: 50%;
  margin: auto;
  height: 30vh;
  /* position: absolute;
  top: 00;
  bottom: 0;
  width: 60%; */
}
</style>

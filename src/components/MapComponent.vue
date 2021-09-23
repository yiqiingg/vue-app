<template>
  <div id="map" />
</template>

<script>
import mapboxgl from 'mapbox-gl'; // or "const mapboxgl = require('mapbox-gl');"

mapboxgl.accessToken =
  'pk.eyJ1IjoieWlxaW5nZ2dnIiwiYSI6ImNrdHhmY2hpMzBqemEybnRobWl1enBza3oifQ.dFwEcCgHZuW6IdqjLu4DnA';
new mapboxgl.Map({
  container: 'map', // container ID
  style: 'mapbox://styles/mapbox/streets-v11', // style URL
  center: [-74.5, 40], // starting position [lng, lat]
  zoom: 9, // starting zoom
});
</script>

<template>
  <div id="shapefile-table">
    <p v-if="shapefile.length < 1" class="empty-table">
      No Files
    </p>
    <table v-else>
      <thead>
        <tr>
          <th>Shapefile name</th>
          <th>Map</th>
          <th>Action</th>
        </tr>
      </thead>
      <!-- <tbody> -->
      <!-- <tr> 
          <td>Richard Hendricks</td>
          <td>richard@piedpiper.com</td>
        </tr>
        <tr>
          <td>Bertram Gilfoyle</td>
          <td>gilfoyle@piedpiper.com</td>
        </tr>
        <tr>
          <td>Dinesh Chugtai</td>
          <td>dinesh@piedpiper.com</td> -->
      <tbody>
        <tr v-for="file in shapefile" :key="file.id">
          <td>{{ file.name }}</td>
          <td :id="'map' + file.id" />
          <td>
            <button @click="checkStatus(file.uploadId)">Status</button>
            <button class="muted-button" @click="createMaps(file.id)">
              Map
            </button>
          </td>
        </tr>
      </tbody>
      <!-- </tr> -->
      <!-- </tbody> -->
    </table>
  </div>
</template>

<script>
const MY_ACCESS_TOKEN =
  'sk.eyJ1IjoieWlxaW5nZ2dnIiwiYSI6ImNrdHhma3ZiYzBrMXQybnRoemFkOTlhMW4ifQ.Fe0XhvYKsc5A9MmE8xD7OQ';
const mbxUploads = require('@mapbox/mapbox-sdk/services/uploads');
const mbxClient = require('@mapbox/mapbox-sdk');
const baseClient = mbxClient({ accessToken: MY_ACCESS_TOKEN });
const uploadsClient = mbxUploads(baseClient);
// let uploadId;
import mapboxgl from 'mapbox-gl';
mapboxgl.accessToken =
  'pk.eyJ1IjoieWlxaW5nZ2dnIiwiYSI6ImNrdTM4c29waDFscDQycG93NDduNXVlOHMifQ.T2MhPxJH1MHmgO-Qc6unjQ';
//   export default {
//     name: 'employee-table',
//   }
export default {
  name: 'shapefile-table',
  props: {
    shapefile: Array,
  },
  data() {
    return {
      editing: null,
    };
  },
  methods: {
    // editMode(employee) {
    //   // this.editing = id
    //   this.cachedEmployee = Object.assign({}, employee);
    //   this.editing = employee.id;
    // },
    checkStatus(uploadId) {
      uploadsClient
        .getUpload({
          uploadId: `${uploadId}`,
        })
        .send()
        .then((response) => {
          const status = response.body;
          console.log(status);
        });
    },
    async createMaps(id) {
      const map = new mapboxgl.Map({
        container: `map${id}`, // container ID
        style: 'mapbox://styles/mapbox/streets-v11', // style URL
        center: [-122.272781, 37.871666], // starting position [lng, lat]
        zoom: 15, // starting zoom
      });
      map.on('load', () => {
        map.addSource('parks', {
          type: 'vector',
          url: `mapbox://yiqingggg.myTileset${id}`,
        });
        map.addLayer({
          id: 'parks',
          type: 'line',
          source: 'parks',
          'source-layer': `UPLOAD${id}`,
          layout: {
            // Make the layer visible by default.
            visibility: 'visible',
            'line-join': 'round',
            'line-cap': 'round',
          },
          paint: {
            'line-color': '#877b59',
            'line-width': 20,
          },
        });
      });
    },
    cancelEdit(employee) {
      Object.assign(employee, this.cachedEmployee);
      this.editing = null;
    },
    editEmployee(employee) {
      if (employee.name === '' || employee.email === '') return;
      this.$emit('edit:employee', employee.id, employee);
      this.editing = null;
    },
  },
};
</script>

<style scoped>
button {
  margin: 0 0.5rem 0 0;
}
</style>

<template>
  <div id="app" class="small-container">
    <h1>Visualize a Shapefile from Berkeley</h1>
    <employee-form @add:shapefile="createUpload" />
    <employee-table
      :employees="employees"
      @delete:employee="deleteEmployee"
      @edit:employee="editEmployee"
    />
    <map-component />
  </div>
</template>

<script>
import dotenv from 'dotenv';

// import mapboxgl from 'mapbox-gl'; // or "const mapboxgl = require('mapbox-gl');"

// mapboxgl.accessToken =
//   'pk.eyJ1IjoieWlxaW5nZ2dnIiwiYSI6ImNrdHhmY2hpMzBqemEybnRobWl1enBza3oifQ.dFwEcCgHZuW6IdqjLu4DnA';
// new mapboxgl.Map({
//   container: 'map', // container ID
//   style: 'mapbox://styles/mapbox/streets-v11', // style URL
//   center: [-74.5, 40], // starting position [lng, lat]
//   zoom: 9, // starting zoom
// });

dotenv.config();
import EmployeeTable from '@/components/EmployeeTable.vue';
import EmployeeForm from '@/components/EmployeeForm.vue';
import MapComponent from '@/components/MapComponent.vue';
export default {
  name: 'app',
  components: {
    EmployeeTable,
    EmployeeForm,
    MapComponent,
  },
  data() {
    return {
      shapefile: [],
    };
  },
  methods: {
    async addShapefile(newShapefile) {
      //add shapefile to array of shapefile
      const id = this.shapefile.length;
      newShapefile.id = id;
      this.shapefile = [...this.shapefile, newShapefile];
      console.log(newShapefile, this.shapefile);
      console.log('starting upload...');
      const getCredentials = () => {
        return uploadsClient
          .createUploadCredentials()
          .send()
          .then((response) => response.body);
      };
      const putFileOnS3 = (credentials) => {
        const s3 = new AWS.S3({
          accessKeyId: credentials.accessKeyId,
          secretAccessKey: credentials.secretAccessKey,
          sessionToken: credentials.sessionToken,
          region: 'us-east-1',
        });
        return s3
          .putObject({
            Bucket: credentials.bucket,
            Key: credentials.key,
            Body: this.selectedFile,
          })
          .promise();
      };
      const credentials = await getCredentials();
      putFileOnS3(credentials);
      const myUsername = 'yiqingggg';
      const myTileset = `myTileset0`;
      console.log(credentials);
      uploadsClient
        .createUpload({
          tileset: `${myUsername}.${myTileset}`,
          url:
            'https://tilestream-tilesets-production.s3.amazonaws.com/f9/_pending/ojdxb7tykp82x1s3nceqyxtkc/yiqingggg',
          name: `UPLOAD0`,
        })
        .send()
        .then((response) => {
          const upload = response.body;
          console.log(upload, upload.tileset);
          tilesetid = upload.tileset;
          this.shapefile[id].uploadId = upload.id;
        });
      console.log('done', this.shapefile);

      // await setTimeout(() => {
      //   this.createMaps(newShapefile.id);
      // }, 10000);
      // const map = new mapboxgl.Map({
      //   container: 'map-component', // container ID
      //   style: 'mapbox://styles/mapbox/streets-v11', // style URL
      //   center: [-122.272781, 37.871666], // starting position [lng, lat]
      //   zoom: 15, // starting zoom
      // });
      console.log(tilesetid);
      // map.on('load', () => {
      //   map.addSource('parks', {
      //     type: 'vector',
      //     url: `mapbox://yiqingggg.myTileset5`,
      //   });
      //   map.addLayer({
      //     id: 'parks',
      //     type: 'line',
      //     source: 'parks',
      //     'source-layer': 'UPLOAD5',
      //     layout: {
      //       // Make the layer visible by default.
      //       visibility: 'visible',
      //       'line-join': 'round',
      //       'line-cap': 'round',
      //     },
      //     paint: {
      //       'line-color': '#877b59',
      //       'line-width': 20,
      //     },
      //   });
      // });
      // const map = new mapboxgl.Map({
      //   container: 'map-component', // container ID
      //   style: 'mapbox://styles/mapbox/streets-v11', // style URL
      //   center: [-122.272781, 37.871666], // starting position [lng, lat]
      //   zoom: 15, // starting zoom
      // });
      console.log(tilesetid);
    },
    // async addEmployee(employee) {
    //   try {
    //     const response = await fetch(
    //       'https://jsonplaceholder.typicode.com/users',
    //       {
    //         method: 'POST',
    //         body: JSON.stringify(employee),
    //         headers: { 'Content-type': 'application/json; charset=UTF-8' },
    //       }
    //     );
    //     const data = await response.json();
    //     this.employees = [...this.employees, data];
    //   } catch (error) {
    //     console.error(error);
    //   }
    // },
    // async getEmployees() {
    //   // const lastId =
    //   //   this.employees.length > 0
    //   //     ? this.employees[this.employees.length - 1].id
    //   //     : 0;
    //   // const id = lastId + 1;
    //   // const newEmployee = { ...employee, id };
    //   // this.employees = [...this.employees, newEmployee];
    //   try {
    //     const response = await fetch(
    //       'https://jsonplaceholder.typicode.com/users'
    //     );
    //     const data = await response.json();
    //     this.employees = data;
    //   } catch (error) {
    //     console.error(error);
    //   }
    // },
    // deleteEmployee(id) {
    //   this.employees = this.employees.filter(
    //     employee => employee.id !== id
    //   )
    // },
    // async deleteEmployee(id) {
    //   try {
    //     await fetch(`https://jsonplaceholder.typicode.com/users/${id}`, {
    //       method: 'DELETE',
    //     });
    //     this.employees = this.employees.filter(
    //       (employee) => employee.id !== id
    //     );
    //   } catch (error) {
    //     console.error(error);
    //   }
    // },
  },
};
</script>

<style>
body {
  margin: 0;
  padding: 0;
}
#map {
  position: absolute;
  top: 0;
  bottom: 0;
  width: 100%;
}
button {
  background: #009435;
  border: 1px solid #009435;
}
.small-container {
  max-width: 680px;
}
</style>

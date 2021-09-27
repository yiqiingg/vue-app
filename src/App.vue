<template>
  <div id="app" class="small-container">
    <h1>Visualize a Shapefile from Berkeley</h1>

    <shapefile-form @add:shapefile="addShapefile" />
    <div id="map-component" />
    <shapefile-table :shapefile="shapefile" />
  </div>
</template>

<script>
let tilesetid;
const MY_ACCESS_TOKEN =
  'sk.eyJ1IjoieWlxaW5nZ2dnIiwiYSI6ImNrdHhma3ZiYzBrMXQybnRoemFkOTlhMW4ifQ.Fe0XhvYKsc5A9MmE8xD7OQ';
const mbxUploads = require('@mapbox/mapbox-sdk/services/uploads');
const mbxClient = require('@mapbox/mapbox-sdk');
const baseClient = mbxClient({ accessToken: MY_ACCESS_TOKEN });
const uploadsClient = mbxUploads(baseClient);
// let uploadId;
import mapboxgl from 'mapbox-gl';
const AWS = require('aws-sdk');
mapboxgl.accessToken =
  'pk.eyJ1IjoieWlxaW5nZ2dnIiwiYSI6ImNrdTM4c29waDFscDQycG93NDduNXVlOHMifQ.T2MhPxJH1MHmgO-Qc6unjQ';
import ShapefileTable from '@/components/ShapefileTable.vue';
import ShapefileForm from '@/components/ShapefileForm.vue';
export default {
  name: 'app',
  components: {
    ShapefileForm,
    ShapefileTable,
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
    // async editEmployee(id, updatedEmployee) {
    //   // this.employees = this.employees.map(employee =>
    //   //   employee.id === id? updatedEmployee : employee
    //   // )
    //   try {
    //     const response = await fetch(
    //       `https://jsonplaceholder.typicode.com/users/${id}`,
    //       {
    //         method: 'PUT',
    //         body: JSON.stringify(updatedEmployee),
    //         headers: { 'Content-type': 'application/json; charset=UTF-8' },
    //       }
    //     );
    //     const data = await response.json();
    //     this.employees = this.employees.map((employee) =>
    //       employee.id === id ? data : employee
    //     );
    //   } catch (error) {
    //     console.error(error);
    //   }
    // },
  },
};
</script>

<style>
button {
  background: #009435;
  border: 1px solid #009435;
}
.small-container {
  max-width: 680px;
}
</style>

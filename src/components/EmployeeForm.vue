<template>
  <div id="employee-form">
    <label>Shapefile upload</label>
    <!-- <form @submit.prevent="checkUploadStatus">
      <button>Check</button>
    </form> -->
    <form
      class="upload"
      @submit.prevent="handleFileSubmit"
      @change="previewFiles"
    >
      <input type="file" name="uploadFile" accept=".zip" required />
      <br /><br />
      <input type="submit" />
    </form>

    <button class="btn btn-info" @click="onPickFile">
      Upload profile picture
    </button>
    <input
      type="file"
      style="display: none"
      ref="fileInput"
      @change="onFilePicked"
    />

    <form @submit.prevent="handleSubmit">
      <!-- <input type="text" /> -->
      <label>Employee name</label>
      <input
        ref="first"
        type="text"
        :class="{ 'has-error': submitting && invalidName }"
        v-model="employee.name"
        @focus="clearStatus"
        @keypress="clearStatus"
      />
      <label>Employee Email</label>
      <!-- <input type="text" /> -->
      <!-- <input v-model="employee.email" type="text" /> -->
      <input
        type="text"
        :class="{ 'has-error': submitting && invalidEmail }"
        v-model="employee.email"
        @focus="clearStatus"
        @keypress="clearStatus"
      />
      <p v-if="error && submitting" class="error-message">
        Please fill out all required fields.
      </p>
      <p v-if="success" class="success-message">
        Employee successfully added!
      </p>
      <button>Add Employee</button>
    </form>
    <div id="map-component" />
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
  'pk.eyJ1IjoieWlxaW5nZ2dnIiwiYSI6ImNrdHkwM2w1cTJ5dWgydnBtdWx4MXFlN3EifQ.WoSz2Zn-h7SkdDmouxC0XQ';
export default {
  name: 'employee-form',
  data() {
    return {
      submiting: false,
      error: false,
      success: false,
      employee: {
        name: '',
        email: '',
      },
      selectedFile: null,
    };
  },
  methods: {
    async onPickFile() {
      this.$refs.fileInput.click();
    },
    // async checkUploadStatus() {
    //   await uploadsClient
    //     .getUpload({
    //       uploadId: uploadId,
    //     })
    //     .send()
    //     .then((response) => {
    //       const status = response.body;
    //       console.log(status);
    //     });
    // },
    async handleFileSubmit() {
      console.log('starting upload...');
      // console.log(event.target.files);
      // import * as fs from 'fs';
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
            // Body: fs.createReadStream('/path/to/file.mbtiles')
          })
          .promise();
      };
      // console.log(await getCredentials());
      // let credentials = await getCredentials();
      // console.log(credentials);
      // credentials = putFileOnS3(credentials);
      // response.then(console.log);
      // response.then((res) => console.log(res));
      const credentials = await getCredentials();
      putFileOnS3(credentials);
      const myUsername = 'yiqingggg';
      const myTileset = 'myTileset2';
      // const credentials = uploadsClient.createUploadCredentials();
      console.log(credentials);
      uploadsClient
        .createUpload({
          tileset: `${myUsername}.${myTileset}`,
          url:
            'https://tilestream-tilesets-production.s3.amazonaws.com/f9/_pending/ojdxb7tykp82x1s3nceqyxtkc/yiqingggg',
          name: 'JMLQ UPLOAD1',
        })
        .send()
        .then((response) => {
          const upload = response.body;
          console.log(upload, upload.tileset);
          tilesetid = upload.tileset;
          // uploadId = response.body.id;
        });
      // await uploadsClient
      //   .getUpload({
      //     uploadId: uploadId,
      //   })
      //   .send()
      //   .then((response) => {
      //     const status = response.body;
      //     console.log(status);
      //   });
      console.log('done');
      const map = new mapboxgl.Map({
        container: 'map-component', // container ID
        style: 'mapbox://styles/mapbox/streets-v11', // style URL
        center: [-122.272781, 37.871666], // starting position [lng, lat]
        zoom: 15, // starting zoom
      });
      console.log(tilesetid);
      map.on('load', () => {
        map.addSource('parks', {
          type: 'vector',
          url: `mapbox://yiqingggg.myTileset2`,
        });
        map.addLayer({
          id: 'parks',
          type: 'line',
          source: 'parks',
          'source-layer': 'JMLQ UPLOAD1',
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
    // async onFilePicked (event) {
    //   const files = event.target.files
    //   let filename = files[0].name
    //   const fileReader = new FileReader()
    //   fileReader.addEventListener('load', () => {
    //     this.imageUrl = fileReader.result
    //   })
    //   fileReader.readAsDataURL(files[0])
    //   this.image = files[0]
    //   console.log(filename);
    //   await handleFileSubmit(this.image);
    // },
    async previewFiles(event) {
      this.selectedFile = event.target.files[0];
    },
    handleSubmit() {
      console.log('testing handleSubmit');
      this.submitting = true;
      this.clearStatus();
      if (this.invalidName || this.invalidEmail) {
        this.error = true;
        return;
      }
      this.$emit('add:employee', this.employee);
      this.$refs.first.focus();
      this.employee = {
        name: '',
        email: '',
      };
      this.error = false;
      this.success = true;
      this.submitting = false;
    },
    clearStatus() {
      this.success = false;
      this.error = false;
    },
  },
  computed: {
    invalidName() {
      return this.employee.name === '';
    },
    invalidEmail() {
      return this.employee.email === '';
    },
  },
};
//   export default {
//     name: 'employee-form',
//     data() {
//       return {
//         employee: {
//           name: '',
//           email: '',
//         },
//       }
//     },
//     methods: {
//         handleSubmit() {
//             console.log('testing handleSubmit')
//         },
//     },
//   }
</script>

<style scoped>
form {
  margin-bottom: 2rem;
}
[class*='-message'] {
  font-weight: 500;
}
.error-message {
  color: #d33c40;
}
.success-message {
  color: #32a95d;
}
</style>

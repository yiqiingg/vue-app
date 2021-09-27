<template>
  <div id="shapefile-form">
    <label>Shapefile upload</label>
    <form
      class="upload"
      @submit.prevent="handleFileSubmit"
      @change="previewFiles"
    >
      <label>File Name</label>
      <input v-model="shapefiles.name" type="text" />
      <input type="file" name="uploadFile" accept=".zip" required />
      <br /><br />
      <input type="submit" />
    </form>
    <div id="map-component" />
  </div>
</template>

<script>
// let tilesetid;
// const MY_ACCESS_TOKEN =
//   'sk.eyJ1IjoieWlxaW5nZ2dnIiwiYSI6ImNrdHhma3ZiYzBrMXQybnRoemFkOTlhMW4ifQ.Fe0XhvYKsc5A9MmE8xD7OQ';
// const mbxUploads = require('@mapbox/mapbox-sdk/services/uploads');
// const mbxClient = require('@mapbox/mapbox-sdk');
// const baseClient = mbxClient({ accessToken: MY_ACCESS_TOKEN });
// const uploadsClient = mbxUploads(baseClient);
// // let uploadId;
// import mapboxgl from 'mapbox-gl';
// const AWS = require('aws-sdk');
// mapboxgl.accessToken =
//   'pk.eyJ1IjoieWlxaW5nZ2dnIiwiYSI6ImNrdHkwM2w1cTJ5dWgydnBtdWx4MXFlN3EifQ.WoSz2Zn-h7SkdDmouxC0XQ';
export default {
  name: 'shapefile-form',
  data() {
    return {
      submiting: false,
      error: false,
      success: false,
      shapefiles: {
        name: '',
        id: '',
      },
      selectedFile: null,
    };
  },
  methods: {
    // async onPickFile() {
    //   this.$refs.fileInput.click();
    // },
    async handleFileSubmit() {
      this.submitting = true;
      this.clearStatus();
      this.$emit('add:shapefile', this.shapefiles);
      this.shapefiles = {
        name: '',
        id: '',
      };
      this.error = false;
      this.success = true;
      this.submitting = false;
    },
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

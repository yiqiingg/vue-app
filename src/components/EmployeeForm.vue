<template>
  <div id="employee-form">
    <form @submit.prevent="createFileUpload">
      <label>Shapefile name</label>
      <input v-model="shapefile.name" type="text" />
      <label>Upload Shapefile</label>
      <input type="file" @change="uploadFileSave" name="uploadFile" required />
      <p v-if="error && submitting" class="error-message">
        ❗Please fill out all required fields
      </p>
      <p v-if="success" class="success-message">
        ✅ File successfully added
      </p>
      <button>Add File</button>
    </form>
  </div>
</template>

<script>
export default {
  name: 'employee-form',
  data() {
    return {
      submitting: false,
      error: false,
      success: false,
      shapefile: {
        name: '',
        file: '',
      },
    };
  },
  computed: {
    invalidName() {
      console.log(this.shapefile.name);
      return this.shapefile.name === '';
    },
  },
  methods: {
    uploadFileSave(event) {
      console.log(event.target.files[0]);
      this.shapefile.file = event.target.files[0];
    },
    async createFileUpload() {
      console.log(this.shapefile);
      this.submitting = true;
      this.clearStatus();
      const MY_ACCESS_TOKEN =
        'sk.eyJ1IjoieWlxaW5nZ2dnIiwiYSI6ImNrdG51MXM3OTA2OW4zMHA5dDZmdjVoZTUifQ.q2dxmwAQjk9fg9LABjU97g';
      const mbxUploads = require('@mapbox/mapbox-sdk/services/uploads');
      const mbxClient = require('@mapbox/mapbox-sdk');
      const baseClient = mbxClient({ accessToken: MY_ACCESS_TOKEN });
      const uploadsClient = mbxUploads(baseClient);

      const AWS = require('aws-sdk');
      const getCredentials = () => {
        return uploadsClient
          .createUploadCredentials()
          .send()
          .then((response) => response.body);
      };
      this.shapefile = {
        name: '',
        file: '',
      };
      this.error = false;
      this.success = true;
      this.submitting = false;
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
            Body: this.shapefile.file,
          })
          .promise();
      };
      const credentials = await getCredentials();
      putFileOnS3(credentials);
      console.log(credentials);
      // const myUsername = 'yiqingggg';
      // const myTileset = 'myTileset';

      await uploadsClient
        .createUpload({
          tileset: `yiqingggg.myTileset`,
          url: credentials.url,
          name: `test`,
        })
        .send()
        .then((response) => {
          const upload = response.body;
          console.log(upload);
        });
      console.log('done');
    },

    clearStatus() {
      this.success = false;
      this.error = false;
    },
  },
};
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

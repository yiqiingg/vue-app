<template>
  <div id="app" class="small-container">
    <h1>Visualize a Shapefile from Berkeley</h1>
    <employee-form @add:shapefile="createUpload" />
    <employee-table
      :employees="employees"
      @delete:employee="deleteEmployee"
      @edit:employee="editEmployee"
    />
  </div>
</template>

<script>
import dotenv from 'dotenv';

dotenv.config();
import EmployeeTable from '@/components/EmployeeTable.vue';
import EmployeeForm from '@/components/EmployeeForm.vue';
export default {
  name: 'app',
  components: {
    EmployeeTable,
    EmployeeForm,
  },
  data() {
    return {
      shapefiles: [
        {
          id: 1,
          name: 'Richard Hendricks',
          file: 'richard@piedpiper.com',
        },
        {
          id: 2,
          name: 'Bertram Gilfoyle',
          file: 'gilfoyle@piedpiper.com',
        },
        {
          id: 3,
          name: 'Dinesh Chugtai',
          file: 'dinesh@piedpiper.com',
        },
      ],
    };
  },
  mounted() {
    this.getEmployees();
  },
  methods: {
    async addShapefile(file) {
      try {
        const response = await fetch(
          'https://api.mapbox.com/uploads/v1/yiqingggg/credentials?access_token=sk.eyJ1IjoieWlxaW5nZ2dnIiwiYSI6ImNrdG51MXM3OTA2OW4zMHA5dDZmdjVoZTUifQ.q2dxmwAQjk9fg9LABjU97g',
          {
            method: 'POST',
            body: JSON.stringify(file),
            headers: { 'Content-type': 'application/json; charset=UTF-8' },
          }
        );
        const data = await response.json();
        console.log(data, 'hiii this is the data');
        return data;
      } catch (error) {
        console.error(error);
      }
    },
    // async stageShapefile(file) {
    //   try {
    //     const response = await fetch(
    //       `http://${credentials.bucket}.s3.amazonaws.com/${credentials.key}`,
    //       {
    //         method: 'PUT',
    //         body: JSON.stringify(file),
    //         headers: { 'Content-type': 'application/json; charset=UTF-8' },
    //       }
    //     );
    //     const data = await response.json();
    //     console.log(data, 'hiii this is the data');
    //     return data;
    //   } catch (error) {
    //     console.error(error);
    //   }
    // },
    // async createFileUpload(file) {
    //   try {
    //     console.log(file)
    //     const MY_ACCESS_TOKEN = 'sk.eyJ1IjoieWlxaW5nZ2dnIiwiYSI6ImNrdG51MXM3OTA2OW4zMHA5dDZmdjVoZTUifQ.q2dxmwAQjk9fg9LABjU97g';
    //     const mbxUploads = require('@mapbox/mapbox-sdk/services/uploads');
    //     const mbxClient = require('@mapbox/mapbox-sdk');
    //     const baseClient = mbxClient({ accessToken: MY_ACCESS_TOKEN });
    //     const uploadsClient = mbxUploads(baseClient);

    //     const AWS = require('aws-sdk');
    //     const getCredentials = () => {
    //         return uploadsClient
    //           .createUploadCredentials()
    //           .send()
    //           .then(response => response.body);
    //     }
    //     const putFileOnS3 = (credentials) => {
    //     const s3 = new AWS.S3({
    //     accessKeyId: credentials.accessKeyId,
    //     secretAccessKey: credentials.secretAccessKey,
    //     sessionToken: credentials.sessionToken,
    //     region: 'us-east-1'
    //   });
    //   return s3.putObject({
    //     Bucket: credentials.bucket,
    //     Key: credentials.key,
    //     Body: this.file,
    //   }).promise();
    // };

    // const credentials = await getCredentials();
    // putFileOnS3(credentials);
    // // const myUsername = 'yiqingggg';
    // // const myTileset = 'myTileset';

    // uploadsClient.createUpload({
    //     tileset: `yiqingggg.myTileset`,
    //     url: credentials.url,
    //     name: `${this.file.name}`,
    //   })
    //     .send()
    //     .then(response => {
    //       const upload = response.body;
    //       console.log(upload);
    //     });
    //   console.log('done')
    //   } catch (error) {
    //     console.error(error);
    //   } 
    // },
    async deleteEmployee(id) {
      try {
        await fetch(`https://jsonplaceholder.typicode.com/users/${id}`, {
          method: 'DELETE',
        });
        this.employees = this.employees.filter(
          (employee) => employee.id !== id
        );
      } catch (error) {
        console.error(error);
      }
    },
    async editEmployee(id, updatedEmployee) {
      try {
        const response = await fetch(
          `https://jsonplaceholder.typicode.com/users/${id}`,
          {
            method: 'PUT',
            body: JSON.stringify(updatedEmployee),
            headers: { 'Content-type': 'application/json; charset=UTF-8' },
          }
        );
        const data = await response.json();
        this.employees = this.employees.map((employee) =>
          employee.id === id ? data : employee
        );
      } catch (error) {
        console.error(error);
      }
    },
    async getEmployees() {
      try {
        const response = await fetch(
          'https://jsonplaceholder.typicode.com/users'
        );
        const data = await response.json();
        this.employees = data;
      } catch (error) {
        console.error(error);
      }
    },
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

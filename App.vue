<template>
  <div id="app" class="small-container">
    <h1>Visualize a Shapefile from Berkeley</h1>
    <employee-form @add:shapefile="addShapefile" />
    <employee-table
      :employees="employees"
      @delete:employee="deleteEmployee"
      @edit:employee="editEmployee"
    />
  </div>
</template>

<script>
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
        this.shapefiles = [...this.shapefiles, data];
      } catch (error) {
        console.error(error);
      }
    },
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

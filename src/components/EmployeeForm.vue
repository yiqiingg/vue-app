<template>
  <div id="employee-form">
    <form @submit.prevent="handleSubmit">
      <label>Shapefile name</label>
      <input :class="{ 'has-error': submitting && invalidEmail }" type="text" />
      <label>Upload Shapefile</label>
      <input
        :class="{ 'has-error': submitting && invalidEmail }"
        type="file"
        name="uploadFile"
        accept=".shp"
        required
      />
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
      console.log(this.employee.name);
      return this.employee.name === '';
    },

    invalidEmail() {
      return this.employee.email === '';
    },
  },
  methods: {
    handleSubmit() {
      this.submitting = true;
      this.clearStatus();

      // if (this.invalidName || this.invalidEmail) {
      //   this.error = true;
      //   return;
      // }

      this.$emit('add:shapefile', this.shapefile);
      this.$refs.first.focus();
      this.shapefile = {
        name: '',
        file: '',
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

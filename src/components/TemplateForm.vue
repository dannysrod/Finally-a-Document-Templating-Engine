<template>
  <form @submit.prevent="onSubmit">
    <div v-for="(field, index) in template.fields" :key="index">
      <label :for="field.key">{{ field.label }}</label>
      <input v-model="formData[field.key]" :id="field.key" required />
    </div>
    <button type="submit">Submit</button>
  </form>
</template>

<script>
export default {
  props: ['template'],
  data() {
    return {
      formData: {}
    };
  },
  watch: {
    template(newTemplate) {
      this.resetFormData();
    }
  },
  methods: {
    resetFormData() {
      this.formData = this.template.fields.reduce((acc, field) => {
        acc[field.key] = '';
        return acc;
      }, {});
    },
    onSubmit() {
      this.$emit('form-submitted', this.formData);
    }
  },
  mounted() {
    this.resetFormData();
  }
};
</script>

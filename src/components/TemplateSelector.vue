<template>
  <div>
    <label for="template">Choose a template:</label>
    <select v-model="selectedTemplate" @change="onChange">
      <option disabled value="">Please select one</option>
      <option v-for="(template, index) in templates" :key="index" :value="template">{{ template.name }}</option>
    </select>
  </div>
</template>

<script>
export default {
  props: ['templates'],
  data() {
    return {
      selectedTemplate: ''
    };
  },
  methods: {
    onChange() {
      this.$emit('template-selected', this.selectedTemplate);
    }
  }
};
</script>

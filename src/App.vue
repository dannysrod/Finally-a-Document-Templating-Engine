<template>
  <div id="app">
    <h1>Template Form Generator</h1>
    <template-selector :templates="templates" @template-selected="selectTemplate" />
    <template-form v-if="selectedTemplate" :template="selectedTemplate" @form-submitted="submitForm" />
    <a :href="downloadLink" :download="downloadName" v-if="downloadLink">Download Word Document</a>
  </div>
</template>

<script>
import axios from 'axios';
import { Docx } from 'docx';
import Mustache from 'mustache';
import TemplateSelector from './components/TemplateSelector.vue';
import TemplateForm from './components/TemplateForm.vue';

export default {
  components: {
    TemplateSelector,
    TemplateForm
  },
  data() {
    return {
      templates: [
        {
          name: 'Template 1',
          fields: [
            { key: 'field1', label: 'Field 1' },
            { key: 'field2', label: 'Field 2' },
          ],
          url: '/templates/template1.docx'
        },
        // Add more templates here...
      ],
      selectedTemplate: null,
      downloadLink: null,
      downloadName: null
    };
  },
  methods: {
    selectTemplate(template) {
      this.selectedTemplate = template;
    },
    async submitForm(formData) {
      const response = await axios.get(this.selectedTemplate.url, { responseType: 'arraybuffer' });
      const docx = new Docx({ buffer: response.data });

      const renderedText = Mustache.render(docx.text, formData);
      const newDocx = docx.replaceText(renderedText);

      const buffer = await newDocx.pack();
      const blob = new Blob([buffer], { type: 'application/vnd.openxmlformats-officedocument.wordprocessingml.document' });
      this.downloadLink = URL.createObjectURL(blob);
      this.downloadName = `filled-${this.selectedTemplate.name}.docx`;
    }
  }
};
</script>

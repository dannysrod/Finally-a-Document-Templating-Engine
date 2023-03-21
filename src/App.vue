<template>
  <div id="app">
    <h1>Template Form Generator</h1>
    <template-selector :templates="templates" @template-selected="selectTemplate" />
    <template-form v-if="selectedTemplate" :template="selectedTemplate" @form-submitted="submitForm" />
    <a :href="downloadLink" :download="downloadName" v-if="downloadLink">Download Word Document</a>
  </div>
</template>

<script lang="ts">
import axios from 'axios';
import Docx from 'docx';
import Mustache from 'mustache';
import TemplateSelector from './components/TemplateSelector.vue';
import TemplateForm from './components/TemplateForm.vue';

// This is a Vue component, which is a single-page application that is used to 
// create a custom form based on a template. The user chooses a template from a 
// list of available templates, and then fills out the form. The form is then 
// submitted, and the template is filled out with the data from the form.
//
// The TemplateSelector component is used to select a template, and the 
// TemplateForm component is used to fill out the form.
//
// The data() method sets up the available templates, and initializes the 
// selectedTemplate and downloadLink variables.
//
// The selectTemplate() method is called when a template is selected from the 
// TemplateSelector component. It sets the selectedTemplate variable to the 
// selected template.
//
// The submitForm() method is called when the form is submitted. It downloads 
// the template from the server, fills it out with the data from the form, and 
// then sends the filled out template to the user.

export default {
  components: {
    TemplateSelector,
    TemplateForm
  },
  data() {
    return {
      templates: [
        {
          name: 'Letter',
          fields: [
            { key: 'matterno', label: 'Matter Number' },
            { key: 'date', label: 'Date' },
            { key: 'op.sol', label: 'Opposing Solicitor' },
            { key: 'op.firm', label: 'Opposing Firm' },
            { key: 'op.firm.address', label: 'Opposing Firm Address' },
            { key: 'op.firm.suburb', label: 'Opposing Firm Suburb' },
            { key: 'op.firm.state', label: 'Opposing Firm State' },
            { key: 'op.firm.postcode', label: 'Opposing Firm Postcode' },
            { key: 'op.sol.email', label: 'Opposing Solicitor Email' },
            { key: 'oc.surname', label: 'Own Client Surname' },
            { key: 'op.surname', label: 'Opposing Client Surname' },
          ],
          url: '/templates/letter.docx'
        },
      ],
        // Add more templates here...
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

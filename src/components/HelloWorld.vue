<template>
    <h1>CSR Decoder</h1>
    <div>
      <input type="file" @change="handleFileUpload" />
      <div v-if="fileContent">
        <h3>File Content:</h3>
        <pre>{{ fileContent }}</pre>
        <h3>Decoded CSR:</h3>
        <pre>{{ decodedCSR }}</pre>
      </div>
    </div>
  </template>
  
  <script>
  import forge from 'node-forge';
  
  export default {
    data() {
      return {
        fileContent: null,
        decodedCSR: null
      };
    },
    methods: {
      handleFileUpload(event) {
        const file = event.target.files[0];
        if (file) {
          const reader = new FileReader();
          reader.onload = (e) => {
            this.fileContent = e.target.result;
  
            // Decode the CSR
            this.decodeCSR();
          };
          reader.readAsText(file);
        }
      },
      decodeCSR() {
        try {
          const csr = forge.pki.certificationRequestFromPem(this.fileContent);
          const csrAttributes = csr.subject.attributes;
  
          const decoded = {};
          csrAttributes.forEach(attr => {
            decoded[attr.name] = attr.value;
          });
  
          this.decodedCSR = JSON.stringify(decoded, null, 2);
        } catch (error) {
          console.error('Error decoding CSR:', error);
          this.decodedCSR = 'Error decoding CSR. Please check the file format.';
        }
      }
    }
  };
  </script>
  

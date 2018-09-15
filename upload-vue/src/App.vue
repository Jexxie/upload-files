<template>
  <div class="section">
    <div class="container">

      <div class="columns">
        <div class="column">
          <simple-upload />
        </div>
        <div class="column">
          <multiple-uploads />
        </div>
      </div>



    </div>
  </div>
</template>

<script>
import SimpleUpload from './SimpleUpload.vue'
import MultipleUploads from './MultipleUploads.vue'

export default {
  name: "App",
  components: { SimpleUpload, MultipleUploads }
}
</script>

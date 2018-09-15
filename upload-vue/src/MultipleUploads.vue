<template>
  <form @submit.prevent="sendFile" enctype="multipart/form-data">

    <p class="title">Multiple Files Upload</p>

    <div v-if="message"
      :class="`message ${error ? 'is-danger' : 'is-success'}`"
    >
      <div class="message-body">{{message}}</div>
    </div>

    <div class="field">

     <div class="file is-boxed is-warning">
       <label class="file-label">  
        <input
          multiple
          type="file"
          ref="files"
          @change="selectFile"
          class="file-input"
        />

        <span class="file-cta">
          <span class="file-icon">
            <i class="fas fa-upload"></i>
          </span>
          <span class="file-label">
            Choose some files...
          </span>
        </span>


       </label>
     </div>
    </div>

    <div class="field">
      <div v-for="(file, index) in files" :key="index" 
        :class="`level ${file.invalidMessage && 'has-text-danger'}`">
        <div class="level-left">
          <div class="level-item">
            {{file.name}}
            <span v-if="file.invalidMessage">&nbsp; - {{file.invalidMessage}}</span>
          </div>
        </div>
        <div class="level-right">
          <div class="level-item">
            <a @click.prevent="deleteFile(index)" class="delete"></a>
          </div>
        </div>
      </div>
    </div>

    <div class="field">
      <button class="button is-info">Send</button>
    </div>

  </form>
</template>

<script>
import axios from 'axios'
import _ from 'lodash'

export default {
  name: "MultipleUploads",

  data() {
    return {
      files: [],
      uploadFiles: [],
      message: "",
      error: false
    }
  },

  methods: {
    selectFile() {
      const files = this.$refs.files.files
      this.uploadFiles = [...this.uploadFiles, ...files]
      // console.log(this.files)

      this.files = [
        ...this.files,
        ..._.map(files, file => ({
          name: file.name,
          size: file.size,
          type: file.type,
          invalidMessage: this.validate(file)
        }))
      ]
      
      // this.files = this.files.map(file => ({
      //   name: file.name,
      //   size: file.size,
      //   type: file.type,
      //   invalidMessage: this.validate(file)
      // }))
    },

    deleteFile(index) {
      this.files.splice(index, 1)
      this.uploadFiles.splice(index, 1)
    },

    validate(file) {
      const MAX_SIZE = 200 * 1024
      const allowedTypes = ["image/jpeg", "image/png", "image/gif"]

      if (file.size > MAX_SIZE) {
        return `Too large. Max size is ${MAX_SIZE / 1024}Kb`
      }
      if (!allowedTypes.includes(file.type)) {
        return "Only images are allowed"
      }
      return ""
    },

    async sendFile() {
      const formData = new FormData()
      _.forEach(this.uploadFiles, file => {
        if (this.validate(file) === "") {
          formData.append('files', file)
        }
      })

      try {
        await axios.post('/multiple', formData)
        this.message = "Files has been uploaded"
        this.files = []
        this.uploadfiles = []
      } catch(err) {
        this.message = err.response.data.error
        this.error = true
      }
    }
  }
}
</script>

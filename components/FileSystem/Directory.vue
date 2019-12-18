<template>
  <div class="text-xs">
    <div class="directory-name flex cursor-pointer" v-on:click="toggleOpen()">
      <div class="chevrons" v-if="directory.files.length !== 0 && directory.directories.length !== 0">
        <chevron-right-icon class="w-3" v-if="!open"/>
        <chevron-down-icon class="w-3" v-if="open"/>
      </div>
      <folder-icon class="ml-1 w-3"/>
      <div class="ml-1 relative" style="top: 3px;">{{ directory.name }}</div>
    </div>

    <div class="directory-contents" v-if="open">
      <div class="directory" v-for="dir in directory.directories">
        <directory v-bind:directory="dir" class="ml-4" v-on:changeCode="changeCode"></directory>
      </div>
      <div class="files cursor-pointer">
        <div class="file ml-4 flex" v-for="file in directory.files" v-on:click="changeCode(file.code)"  style="color: #E6D6A8">
          <file-icon class="ml-1 w-3"/>
          <div class="ml-1 relative" style="top: 4px;">{{ file.name }}</div>
        </div>
      </div>
    </div>
  </div>


</template>

<script>
  import { ChevronRightIcon, ChevronDownIcon, FolderIcon, FileIcon } from 'vue-feather-icons'

  export default {
    name: 'directory',

    props: {
      directory: Object
    },

    components: {
      ChevronRightIcon,
      ChevronDownIcon,
      FolderIcon,
      FileIcon
    },

    mounted () {
      if (this.directory !== undefined && this.directory.open !== undefined) {
        this.open = this.directory.open
      }
    },


    data () {
      return {
        open: false
      }
    },

    methods: {
      toggleOpen() {
        this.open = !this.open
      },

      changeCode(newCode) {
        this.$emit('changeCode', newCode)
      }
    }
  }
</script>

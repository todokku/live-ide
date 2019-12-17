<template>
  <div>
    <div class="ide-container m-10">
      <div class="ide-header flex w-full py-4 rounded-t-lg px-6" style="background: #151718">
        <div class="ide-fake-buttons flex w-12 justify-between">
          <div class="fake-button rounded-full w-3 h-3" style="background: #FF5F56"></div>
          <div class="fake-button rounded-full w-3 h-3" style="background: #FFBD2E"></div>
          <div class="fake-button rounded-full w-3 h-3" style="background: #27C93F"></div>
        </div>
      </div>
      <div class="ide-body flex">
        <div class="ide-files py-3 px-3" style="width: 230px; background: #151718; color: #E6CD5E">

          <!-- directories below -->
          <div v-for="directory in directories" class="directory text-xs">
            <div class="directory-name flex cursor-pointer" v-on:click="toggleDirectory(directory.id)">
              <chevron-right-icon class="w-3" v-if="!directoryOpen(directory.id)"/>
              <chevron-down-icon class="w-3" v-if="directoryOpen(directory.id)"/>
              <folder-icon class="ml-1 w-3"/>
              <div class="ml-1 relative" style="top: 3px;">{{ directory.name }}</div>
            </div>
            <div class="directory-items-container ml-5" v-if="directoryOpen(directory.id) && directory.files.length > 0">
              <div class="directory-file flex cursor-pointer" v-for="file in directory.files" v-on:click="showCode(file.code)">
                <file-icon class="ml-1 w-3"/>
                <div class="ml-1 relative" style="top: 3px;">{{ file.name }}</div>
              </div>
            </div>
          </div>

          <!-- files below -->
          <div class="directory-items-container ml-3 text-xs">
            <div class="directory-file flex cursor-pointer" v-for="basicFile in files" v-on:click="showCode(basicFile.code)">
              <file-icon class="ml-1 w-3"/>
              <div class="ml-1 relative" style="top: 3px;">{{ basicFile.name }}</div>
            </div>
          </div>
        </div>

        <client-only placeholder="Codemirror Loading...">
          <codemirror ref="myCm"
                      :value="code"
                      :options="cmOptions"
                      @ready="onCmReady"
                      @focus="onCmFocus"
                      @input="onCmCodeChange"
                      style="width: calc(100vw - 230px); height: auto">
          </codemirror>
        </client-only>
      </div>
      <div class="ide-footer w-full py-3 shadow-lg rounded-b-lg" style="background: #151718">

      </div>
    </div>
  </div>
</template>

<script>
  import { ChevronRightIcon, ChevronDownIcon, FolderIcon, FileIcon } from 'vue-feather-icons'

  // require styles
  import 'codemirror/lib/codemirror.css'
  import 'codemirror/theme/seti.css'

  export default {
    components: {
      FolderIcon,
      ChevronRightIcon,
      ChevronDownIcon,
      FileIcon
    },

    data () {
      return {
        openDirectories: [],

        directories: [
          {
            id: 'test-directory',
            name: 'test directory',
            directories: [],
            files: [
              {
                name: 'file-in-directory.js',
                code: 'const testDirectory = "test1234";'
              },
              {
                name: 'file-in-directory-2.js',
                code: 'const testDirectory = "test1234";'
              },
              {
                name: 'file-in-directory-3.js',
                code: 'const testDirectory = "test1234";'
              }
            ]
          }
        ],

        files: [
          {
            name: "file-1.js",
            code: 'const liveIde = "test123";'
          }
        ],

        code: 'const a = 10',
        cmOptions: {
          tabSize: 4,
          mode: 'text/javascript',
          theme: 'seti',
          lineNumbers: true,
          line: true
        }
      }
    },

    methods: {
      onCmReady(cm) {
        console.log('the editor is readied!', cm)
      },

      onCmFocus(cm) {
        console.log('the editor is focus!', cm)
      },

      onCmCodeChange(newCode) {
        console.log('this is new code', newCode)
        this.code = newCode
      },

      toggleDirectory (id) {
        if (this.openDirectories.indexOf(id) !== -1) {
          this.openDirectories.splice(this.openDirectories.indexOf(id), 1)
          return
        }

        // Put it in the openDirectories variable
        this.openDirectories.push(id);

        // Vue.js array trick
        this.openDirectories.splice(0,0)
      },

      directoryOpen (id) {
        return this.openDirectories.indexOf(id) !== -1
      },

      showCode (code) {
        this.code = code
      }
    },

    computed: {
      codemirror() {
        return (this.$refs.myCm !== undefined ? this.$refs.myCm.codemirror : false)
      }
    },

    mounted() {
      console.log('this is current codemirror object', this.codemirror)
      // you can use this.codemirror to do something...
    }
  }
</script>

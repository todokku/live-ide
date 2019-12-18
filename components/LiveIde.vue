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
          <div class="directory" v-for="directory in directories" v-bind:key="directory.name">
            <directory v-bind:directory="directory" v-on:changeCode="changeCode"></directory>
          </div>
          <div class="files text-xs cursor-pointer" style="color: #E6D6A8">
            <div class="file flex" v-for="file in files" v-on:click="changeCode(file.code)">
              <file-icon class="ml-1 w-3"/>
              <div class="ml-1 relative" style="top: 3px;">{{ file.name }}</div>
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

  import Directory from '~/components/FileSystem/Directory'

  // require styles
  import 'codemirror/lib/codemirror.css'
  import 'codemirror/theme/seti.css'

  export default {
    components: {
      FolderIcon,
      ChevronRightIcon,
      ChevronDownIcon,
      FileIcon,
      Directory
    },

    data () {
      return {
        socket: null,

        directories: [
          {
            name: 'first directory',
            directories: [
              {
                name: 'first sub directory',
                directories: [
                  {
                    name: 'first sub sub directory',
                    directories: [],
                    files: [],
                    open: true
                  }
                ],
                files: [
                  {
                    name: 'file-in-first-sub-directory.js',
                    code: 'const firstSubFile = "test2";'
                  }
                ],
                open: true
              },
            ],
            files: [
              {
                name: 'file-in-first-directory.js',
                code: 'const firstFile = "test1";'
              }
            ],
            open: false
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

      changeCode (code) {
        this.code = code
      },

      changeDirectories () {
        console.log("changeDirectories() was called");
        this.directories = []
      }
    },

    computed: {
      codemirror() {
        return (this.$refs.myCm !== undefined ? this.$refs.myCm.codemirror : false)
      }
    },

    mounted() {
      this.socket = new WebSocket("ws://localhost:3600/");

      this.socket.onopen = () => {
        console.log("connection opened")
      };

      this.socket.onmessage = (e) => {
        console.log("directories should now disappear");

        let fs = JSON.parse(e.data);

        console.log(fs);

        this.directories = fs['directories'];
        this.directories.splice(0,0);

        this.files = fs['files'];
        this.files.splice(0,0)

      }
    }
  }
</script>

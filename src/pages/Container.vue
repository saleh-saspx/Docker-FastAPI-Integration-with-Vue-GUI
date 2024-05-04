<template>
  <div class="flex h-screen overflow-hidden">

    <!-- Sidebar -->
    <Sidebar :sidebarOpen="sidebarOpen" @close-sidebar="sidebarOpen = false"/>

    <!-- Content area -->
    <div class="relative flex flex-col flex-1 overflow-y-auto overflow-x-hidden">

      <!-- Site header -->
      <Header :sidebarOpen="sidebarOpen" @toggle-sidebar="sidebarOpen = !sidebarOpen"/>

      <main>
        <div class="px-4 sm:px-6 lg:px-8 py-8 w-full max-w-9xl mx-auto">

          <!-- Welcome banner -->
          <div class="relative bg-indigo-200 dark:bg-indigo-500 p-4 sm:p-6 rounded-sm overflow-hidden mb-8">

            <!-- Background illustration -->
            <div class="absolute right-0 top-0 -mt-4 mr-16 pointer-events-none hidden xl:block" aria-hidden="true">
              <svg width="319" height="198" xmlns:xlink="http://www.w3.org/1999/xlink">
                <defs>
                  <path id="welcome-a" d="M64 0l64 128-64-20-64 20z"/>
                  <path id="welcome-e" d="M40 0l40 80-40-12.5L0 80z"/>
                  <path id="welcome-g" d="M40 0l40 80-40-12.5L0 80z"/>
                  <linearGradient x1="50%" y1="0%" x2="50%" y2="100%" id="welcome-b">
                    <stop stop-color="#A5B4FC" offset="0%"/>
                    <stop stop-color="#818CF8" offset="100%"/>
                  </linearGradient>
                  <linearGradient x1="50%" y1="24.537%" x2="50%" y2="100%" id="welcome-c">
                    <stop stop-color="#4338CA" offset="0%"/>
                    <stop stop-color="#6366F1" stop-opacity="0" offset="100%"/>
                  </linearGradient>
                </defs>
                <g fill="none" fill-rule="evenodd">
                  <g transform="rotate(64 36.592 105.604)">
                    <mask id="welcome-d" fill="#fff">
                      <use xlink:href="#welcome-a"/>
                    </mask>
                    <use fill="url(#welcome-b)" xlink:href="#welcome-a"/>
                    <path fill="url(#welcome-c)" mask="url(#welcome-d)" d="M64-24h80v152H64z"/>
                  </g>
                  <g transform="rotate(-51 91.324 -105.372)">
                    <mask id="welcome-f" fill="#fff">
                      <use xlink:href="#welcome-e"/>
                    </mask>
                    <use fill="url(#welcome-b)" xlink:href="#welcome-e"/>
                    <path fill="url(#welcome-c)" mask="url(#welcome-f)" d="M40.333-15.147h50v95h-50z"/>
                  </g>
                  <g transform="rotate(44 61.546 392.623)">
                    <mask id="welcome-h" fill="#fff">
                      <use xlink:href="#welcome-g"/>
                    </mask>
                    <use fill="url(#welcome-b)" xlink:href="#welcome-g"/>
                    <path fill="url(#welcome-c)" mask="url(#welcome-h)" d="M40.333-15.147h50v95h-50z"/>
                  </g>
                </g>
              </svg>
            </div>

            <!-- Content -->
            <div class="relative">
              <h1 class="text-2xl md:text-3xl text-slate-800 dark:text-slate-100 font-bold mb-1">{{ container.name }}
                <span :class="container.status === 'running' ? 'badge-success' : 'badge-error' ">{{
                    container.status
                  }}</span></h1>
              <p class="dark:text-indigo-200">{{ container.id }}</p>
            </div>

          </div>

          <div
              class="flex flex-col col-span-full sm:col-span-6 xl:col-span-4 bg-white dark:bg-slate-800 shadow-lg rounded-sm border border-slate-200 dark:border-slate-700">
            <div class="px-5 p-5">
              <strong>Manage</strong>
              <hr/>
              <br/>
              <p v-if="message"
                 style="background: #6362e1; padding: 2px; border-radius: 5px;width: 100%; color: white; margin-bottom: 10px">
                {{ message }}</p>

              <button @click="status()" :class="container.status === 'running' ? 'badge-error' : 'badge-success'">
                {{ container.status === 'running' ? 'Stop Service' : 'Start Service' }}
              </button>

            </div>
          </div>

          <div
              class="flex flex-col col-span-full sm:col-span-6 xl:col-span-4 bg-white dark:bg-slate-800 shadow-lg rounded-sm border border-slate-200 dark:border-slate-700 mt-4">
            <div class="px-5 p-5">
              <strong>Active Port</strong>
              <hr/>
              <br/>
              <ul v-for="ports in container.port">
                <li v-for="port in ports">
                  {{ selectedServer }}:{{ port.HostPort }}
                </li>
              </ul>
            </div>
          </div>
          <div
              class="flex flex-col col-span-full sm:col-span-6 xl:col-span-4 bg-white dark:bg-slate-800 shadow-lg rounded-sm border border-slate-200 dark:border-slate-700 mt-4">
            <div class="px-5 p-5">
              <strong>Last #10 Logs</strong>
              <button style="margin-left:20px;" @click="this.logs()" class="btn btn-info">Load More Logs</button>
              <hr/>
              <br/>
              <code>
                <ul class="terminal" style="margin-top: 10px">
                  <li v-for="log in container.logs">{{ log }}</li>
                </ul>
              </code>
            </div>
          </div>
        </div>
      </main>

      <Banner/>

    </div>

  </div>
</template>
<style>
.terminal {
  width: 100%;
  margin: 50px auto;
  background-color: #000;
  overflow: auto;
}

.terminal li {
  white-space: pre-wrap;
  word-wrap: break-word;
  color: rgb(79, 176, 40);
  padding: 10px;
}
</style>
<script>
import {ref} from 'vue'
import Sidebar from '../partials/Sidebar.vue'
import axios from "axios";
import Swal from 'sweetalert2'


export default {
  name: 'Dashboard',
  components: {
    Sidebar,
  },
  data() {
    return {
      message: false,
      container: {
        id: null,
        status: '',
        name: 'Teews Service Management',
        port: [],
        logs: []
      },
      selectedServer: ''
    };
  },
  setup() {

    const sidebarOpen = ref(false)

    return {
      sidebarOpen,
    }
  },
  methods: {
    getUrl() {
      if (localStorage.getItem('serverURL')) {
        return localStorage.getItem('serverURL');
      }
      return false
    },
    show() {
      this.loadingEvent()
      axios.get(this.getUrl() + "show/" + this.$route.params.container)
          .then((response) => {
            this.message = false;
            this.container = response.data.data
            Swal.close()
          })
          .catch((error) => {
            this.container = []
            Swal.fire({
              title: 'Error!',
              text: error.message,
              icon: 'error',
              confirmButtonText: 'Ok'
            })
          });
    },
    loadingEvent() {
      return Swal.fire({
        icon: "loading",
        title: "Wait...",
        text: "Get Server Detail",
      });
    },
    status() {
      this.loadingEvent()
      var type = this.container.status === "running" ? 'kill' : "start"
      axios.get(this.getUrl() + type + "/" + this.$route.params.container)
          .then((response) => {
            this.message = false;
            Swal.fire({
              icon: type === "kill" ? "warning" : 'success',
              title: "Server Response",
              text: response.data.message,
              preConfirm: () => {
                this.show()
              }
            });
          })
          .catch((error) => {
            this.container = []
            Swal.fire({
              title: 'Error!',
              text: error.message,
              icon: 'error',
              confirmButtonText: 'Ok',
              preConfirm: () => {
                this.show()
              }
            })
          });
    },
    logs() {
      axios.get(this.getUrl() + "logs/" + this.$route.params.container + "/10")
          .then((response) => {
            this.message = false;
            this.container.logs = response.data.data
          })
          .catch((error) => {
            Swal.fire({
              title: 'Error!',
              text: error.message,
              icon: 'error',
              confirmButtonText: 'Ok'
            })
          });
    }
  },
  created() {
    this.selectedServer = localStorage.getItem('selectedServer') ?? ''
    this.show()
  }
}

</script>
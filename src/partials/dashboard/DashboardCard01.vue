<template>
  <div>
    <label>Base Server : </label>
    <select @change="updateServerURL" v-model="selectedServer">
      <option value="127.0.0.1">127.0.0.1</option>
      <option value="10.33.248.54">10.33.248.54</option>
      <option value="10.33.248.53">10.33.248.53</option>
    </select>
    <select @change="updateServerURL" v-model="port">
      <option value="8001">8001</option>
      <option value="8090">8090</option>
      <option value="8010">8010</option>
    </select>
  </div>
  <div
      class="flex flex-col col-span-full sm:col-span-6 xl:col-span-4 bg-white dark:bg-slate-800 shadow-lg rounded-sm border border-slate-200 dark:border-slate-700">
    <div class="px-5 p-5">
      <p v-if="message"
         style="background: #ce0f0f; padding: 2px; border-radius: 5px;width: 100%; color: white; margin-bottom: 10px">
        {{ message }}</p>
      <table>
        <thead>
        <tr>
          <th>Name</th>
          <th>Status</th>
          <th>Port</th>
          <th>Manage</th>
        </tr>
        </thead>
        <tbody>
        <tr v-for="container in containers">
          <td>{{ container.name }}</td>
          <td>
            <span :class="container.status === 'running' ? 'badge-success' : 'badge-error' ">{{
                container.status
              }}</span>
          </td>
          <td>
            <ul v-for="ports in container.port">
              <li v-for="port in ports">
                {{ selectedServer }}:{{ port.HostPort }}
              </li>
            </ul>
          </td>
          <td>
            <RouterLink class="btn-sm show-btn" :to="container.id">Show</RouterLink> &nbsp;
            <button @click="status(container.id,container.status)"
                    :class="container.status === 'running' ? 'btn-sm btn-red' : 'btn-sm btn-info'">
              {{ container.status === 'running' ? 'Stop' : 'Start' }}
            </button>
          </td>
        </tr>

        </tbody>
      </table>
    </div>
  </div>
</template>
<style>

table {
  width: 100%;
  border-collapse: collapse;
}

th, td {
  border: 1px solid #ddd;
  padding: 8px;
  text-align: center;
}

th {
  background-color: #f2f2f2;
}

button {
  padding: 5px 10px;
  margin: 0 5px;
  font-size: 14px;
}

.show-btn {
  background-color: #5ca640;
  color: white;
  border: none;
  margin: 1px;
  font-weight: bold;
}

.btn-info {
  background-color: #5786ea;
  color: white;
  border: none;
  font-weight: bold;
  margin: 1px;
}

.btn-red {
  background-color: #e16262;
  color: white;
  border: none;
  font-weight: bold;
  margin: 1px;
}

</style>
<script>
import axios from 'axios';
import Swal from "sweetalert2";

export default {
  name: 'DashboardCard01',
  data() {
    return {
      message: false,
      containers: [],
      selectedServer: '127.0.0.1',
      port: 8001,
      serverURL: 'http://127.0.0.1:8001/'
    };
  },
  methods: {

    list() {
      this.loadingEvent()
      axios.get(this.getUrl() + "list")
          .then((response) => {
            this.containers = response.data.containers
            this.message = false
            Swal.close()
          })
          .catch((error) => {
            this.containers = []
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
    getUrl() {
      if (localStorage.getItem('serverURL')) {
        return localStorage.getItem('serverURL');
      }
      return this.serverURL
    },
    status(id, status) {
      var type = status === "running" ? 'kill' : "start"
      this.message = "Processing ...";
      axios.get(this.getUrl() + type + id)
          .then((response) => {
            this.message = false;
            alert(response.data.message);
          })
          .catch((error) => {
            this.containers = []
            Swal.fire({
              title: 'Error!',
              text: error.message,
              icon: 'error',
              confirmButtonText: 'Ok'
            })
          });
      this.list()
    },
    updateServerURL() {
      this.serverURL = `http://${this.selectedServer}:${this.port}/`;
      localStorage.setItem('serverURL', this.serverURL);
      localStorage.setItem('selectedServer', this.selectedServer);
      this.list()
    },
  },
  created() {
    localStorage.setItem('serverURL', this.serverURL);
    localStorage.setItem('selectedServer', this.selectedServer);
    this.list()
  }
}

</script>
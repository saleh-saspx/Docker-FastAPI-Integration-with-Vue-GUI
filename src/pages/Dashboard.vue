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
          <WelcomeBanner/>

          <dashboard-card01/>
        </div>
      </main>

      <Banner/>

    </div>

  </div>
</template>

<script>
import {ref} from 'vue'
import Sidebar from '../partials/Sidebar.vue'
import WelcomeBanner from '../partials/dashboard/WelcomeBanner.vue'
import DashboardCard01 from '../partials/dashboard/DashboardCard01.vue'


export default {
  name: 'Dashboard',
  components: {
    DashboardCard01,
    Sidebar,
    WelcomeBanner,

  },
  setup() {

    const sidebarOpen = ref(false)

    return {
      sidebarOpen,
    }
  }
}
</script>
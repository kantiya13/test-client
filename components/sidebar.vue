<script setup lang="ts">
const route = useRoute()
const sidebar = ref([
  {
    id: 1,
    name: "จัดการแบบทดสอบ",
    path: "/manage-question",
    icon: "mdiFileTableOutline"
  },
  {
    id: 2,
    name: "จัดการผู้ใช้",
    path: "/manage-users",
    icon: "mdiAccount"
  }
])
</script>

<template>
  <div>
    <button data-drawer-target="default-sidebar" data-drawer-toggle="default-sidebar" aria-controls="default-sidebar"
            type="button"
            class="inline-flex items-center p-2 mt-2 ms-3 text-sm text-gray-500 rounded-lg sm:hidden hover:bg-gray-100 focus:outline-none focus:ring-2 focus:ring-gray-200 dark:text-gray-400 dark:hover:bg-gray-700 dark:focus:ring-gray-600">
      <span class="sr-only">Open sidebar</span>
      <svg class="w-6 h-6" aria-hidden="true" fill="currentColor" viewBox="0 0 20 20"
           xmlns="http://www.w3.org/2000/svg">
        <path clip-rule="evenodd" fill-rule="evenodd"
              d="M2 4.75A.75.75 0 012.75 4h14.5a.75.75 0 010 1.5H2.75A.75.75 0 012 4.75zm0 10.5a.75.75 0 01.75-.75h7.5a.75.75 0 010 1.5h-7.5a.75.75 0 01-.75-.75zM2 10a.75.75 0 01.75-.75h14.5a.75.75 0 010 1.5H2.75A.75.75 0 012 10z"></path>
      </svg>
    </button>
    <aside id="default-sidebar"
           class="fixed top-0 left-0 z-40 w-64 h-screen transition-transform -translate-x-full sm:translate-x-0"
           aria-label="Sidebar">
      <div class="h-full px-3 py-4 overflow-y-auto bg-white dark:bg-gray-800">
        <div class="mt-3 mb-3 font-bold text-center">
          ระบบแบบทดสอบ
        </div>
        <ul class="space-y-2 font-medium">
          <li v-for="(item, index) in sidebar" :key="index">
            <NuxtLink :to="item.path">
              <div :class="route.path.includes(item.path)  ? 'bg-blue-600 text-white': ''"
                   class="flex items-center p-2.5 text-gray-900 rounded-xl dark:text-white hover:bg-gray-300 hover:text-white dark:hover:bg-blue-950 group">
                <MdiIcon :icon="item.icon" class="ml-2"/>
                <span class="ms-3">{{ item.name }}</span>
              </div>
            </NuxtLink>
          </li>
        </ul>
      </div>
    </aside>
  </div>
</template>

<style scoped>
.active-sidebar {
  background: #0047e1;
}
</style>
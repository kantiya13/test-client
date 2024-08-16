<script setup lang="ts">
const supabase = useSupabaseClient()
const router = useRouter()
const route = useRoute()
const sidebar = ref([
  {
    id: 1,
    name: "แบบทดสอบ",
    path: "/question",
    icon: "mdiFileDocumentOutline"
  },
  {
    id: 2,
    name: "ผลการทดสอบ",
    path: "/result",
    icon: "mdiFolderCheckOutline"
  }
])

const logout = async () => {
  await supabase.auth.signOut().then(() => {
    router.push('/login')
  })
}
</script>

<template>

  <nav class="nav-box">
    <nav class="nav" >
      <nuxt-link :to="item.path" v-for="(item, index) in sidebar" :key="index">
        <div class="flex flex-row" :class="route.path.includes(item.path) ? 'menu-content-active': 'menu-content'" aria-current="page">
          <mdi-icon :icon="item.icon" class="mt-1 mr-2 ml-2"/>
          <div class="mr-2">{{item.name}}</div>
        </div>
      </nuxt-link>
    </nav>
    <div class="mt-1">
      <div class="action-icon action-icon-delete mr-1" @click="logout">
        <mdi-icon icon="mdiLogout"/>
      </div>
    </div>
  </nav>

</template>

<style scoped>
.nav-box {
  background: #ffffff;
  position: relative;
  display: flex;
  padding: 1rem;
  margin: 1rem;
  border-radius: 10px;
  justify-content: space-between;
}
.menu-content {
  color: #1e293b;
  vertical-align: middle;
  margin: .2rem;
  padding: .5rem;
}
.menu-content:hover {
  color: #FFFFFF;
  vertical-align: middle;
  margin: .2rem;
  padding: .5rem;
  background: #d4d4d8;
  border-radius: 10px;
}
.menu-content-active {
  color: #FFFFFF;
  vertical-align: middle;
  margin: .2rem;
  padding: .5rem;
  background: #2563eb;
  border-radius: 10px;
}
</style>
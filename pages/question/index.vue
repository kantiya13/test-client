<script setup lang="ts">
  import dayjs from "dayjs";

  const supabase = useSupabaseClient()
  const router = useRouter()
  const group_question = ref([])
  const search = ref('')
  onMounted(() => {
    getGroupQuestionList()
  })
  const getGroupQuestionList = async () => {
    try {
      if (search.value) {
        let {data: group, error} = await supabase
            .from('group')
            .select('*')
            .like('title', `%${search.value}%`)
        if (error) throw error
        group_question.value = group
      }else {
        let {data: group, error} = await supabase
            .from('group')
            .select('*')

        if (error) throw error
        group_question.value = group
      }

    } catch (err) {
      throw err
    }
  }
  const startQuestion = (id: string) => {
    router.push(`/question/start-question?group=${id}`).catch(() => {})
  }
  const changeFormatDate = (date: string) => {
    return dayjs(date).format("D/MM/YYYY HH:mm")
  }
  const clearFilterGroupList = () => {
    search.value = ''
    getGroupQuestionList()
  }
</script>

<template>
  <div class="h-full">
    <div class="card">
        <div class="card-body">
            <div class="p-3">
              <div class="text-xl">แบบทดสอบ</div>
              <small>กรุณาเลือกแบบทดสอบที่ท่านต้องการ</small>
              <div class="mt-3">
                <label>ชื่อแบบทดสอบ</label>
                <div class="row">
                  <div class="col-4">
                    <input class="form-control form-control-sm" type="text" v-model="search">
                  </div>
                  <div class="col-auto action-icon action-icon-filter" @click="getGroupQuestionList">
                    <mdi-icon icon="mdiMagnify"/>
                  </div>
                  <div class="col-auto action-icon action-icon-filter ml-1" @click="clearFilterGroupList">
                    <mdi-icon icon="mdiRestore"/>
                  </div>
                </div>
              </div>
              <div v-for="(group, index) in group_question" :key="index" @click="startQuestion(group.id)" v-if="group_question.length > 0">
                <div class="group-question-list">
                  {{group.title}}
                  <div>
                    <small>{{changeFormatDate(group.created_at)}}</small>
                  </div>
                </div>
              </div>
              <div v-else class="text-center m-5">
                ไม่พบข้อมูลในตาราง
              </div>
            </div>
        </div>
    </div>
  </div>
</template>

<style scoped>
.group-question-list {
  margin-top: .5rem;
  background: #FFFFFF;
  border: 1px solid #d4d4d8;
  border-radius: 10px;
  padding: 1rem;
}
.group-question-list:hover {
  cursor: pointer;
  color: #2563eb;
  background: #eff6ff;
  border: 1px solid #2563eb;
  padding: 1rem;
  margin-top: .5rem;
}
</style>
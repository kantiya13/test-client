<script setup lang="ts">
import dayjs from "dayjs";

const supabase = useSupabaseClient()
const router = useRouter()
const group_question = ref([])
onMounted(() => {
  getGroupQuestionList()
})
const getGroupQuestionList = async () => {
  try {
    let {data: group, error} = await supabase
        .from('group')
        .select('*')
    if (error) throw error
    group_question.value = group
  } catch (err) {
    throw err
  }
}
const showResult = (id: string) => {
  router.push(`/question/result?group=${id}`).catch(() => {})
}
</script>

<template>
  <div class="h-full">
    <div class="card">
      <div class="card-body">
        <div class="p-3">
          <small>กรุณาเลือกแบบทดสอบที่ท่านต้องการดูผลการทดสอบ</small>
          <div v-for="(group, index) in group_question" :key="index" @click="showResult(group.id)">
            <div class="group-question-list">
              {{group.title}}
            </div>
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
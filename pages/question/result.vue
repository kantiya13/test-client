<script setup lang="ts">
const supabase = useSupabaseClient()
const route = useRoute()
const router = useRouter()
definePageMeta({
  layout: 'question'
})
const question = ref([])
const user_answer = ref({})
const total_score = ref(0)
const average_score = ref(0)
const title = ref('')
const is_start = ref(false)
onMounted(async () => {
  const user = await supabase.auth.getUser()
  const group_id = route.query.group

  let {data: group, error: errorGroup} = await supabase
      .from('group')
      .select('*')
      .eq('id', group_id)
      .single()
  if (errorGroup) throw errorGroup
  title.value = group?.title

  let {data: user_result, error} = await supabase
      .from('user_answer')
      .select('*')
      .eq('group_id', group_id)
      .eq('user_id', user.data.user.id)

  user_result.forEach(result => {
    user_answer.value[result.question_id] = result
  })
  const {data: questionData} = await supabase
      .from('question')
      .select('*')
      .eq('group_id', group_id)

  if (questionData) {
    question.value = questionData
  }

  const {data: score} = await supabase
      .from('user_score')
      .select('*')
      .eq('group_id', group_id)
      .eq('user_id',  user.data.user.id)
      .order('created_at', { ascending: false })
      .limit(1)
      .single()

  if (score) {
    is_start.value = true
  }else {
    is_start.value = false
  }
  total_score.value = score.score
  average_score.value = (Number(total_score.value) / Number(questionData.length)) * 100
})
const showAllResult = () => {
  router.push(`/result`).catch(() => {})
}
</script>

<template>
  <div class="p-4">
    <div class="card">
      <div class="card-body">
        <div class="p-3">
          <div class="mb-2 text-center text-lg">{{ 'ผลการทดสอบ' }}</div>
          <div class="mb-2 text-center">เรื่อง {{ title }}</div>
          <div class="mt-3">
            <div v-for="(quiz, index) in question" :key="index" class="mt-2 group-question-list" v-if="is_start">
              <div class="row">
                <div class="col">
                  <div>{{ index + 1 }}. {{ quiz.question }}</div>
                  <div class="flex flex-row">
                    <small class="mr-1" :class="user_answer[quiz.id].is_correct ? 'box-success' : 'box-failed'">คำตอบของคุณ:
                      {{ user_answer[quiz.id].answer }}</small>
                    <small class="ml-1 box-correct">คำตอบที่ถูกต้อง: {{ quiz.correct_answer }}</small>
                  </div>
                </div>
                <div class="col-auto">
                  <div v-if="user_answer[quiz.id].is_correct">
                    <mdi-icon icon="mdiCheckCircleOutline" class="text-green-600 mt-1 text-lg"/>
                  </div>
                  <div v-else>
                    <mdi-icon icon="mdiCloseCircleOutline" class="text-red-600 mt-1 text-lg"/>
                  </div>
                </div>
              </div>
            </div>
            <div v-else class="text-center text-xl mt-5">
             <div>
               ยังไม่ได้ทำแบบทดสอบ
             </div>
            </div>
            <div class="row justify-end mt-5" v-if="is_start">
              <div class="col-auto text-right">
                <small>คะแนนรวม</small>
                <div class="text-xl font-bold text-green-600">{{ total_score }}</div>
              </div>
              <div class="col-auto text-right">
                <small>เฉลี่ย</small>
                <div class="text-xl font-bold">{{average_score}}%</div>
              </div>
            </div>
            <div class="text-center mt-5">
              <button type="button" class="btn btn-primary btn-sm ml-1" @click="showAllResult">
                ดูผลการทดสอบทั้งหมด
              </button>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.group-question-list {
  background: #f5f5f4;
  padding: .4rem 1rem .4rem 1rem;
  border-radius: 10px;
}

.box-correct {
  background: #FFFFFF;
  padding: .2rem 1rem .2rem 1rem;
  border-radius: 10px;
}

.box-failed {
  color: #FFFFFF;
  background: #dc2626;
  padding: .2rem 1rem .2rem 1rem;
  border-radius: 10px;
}

.box-success {
  color: #FFFFFF;
  background: #16a34a;
  padding: .2rem 1rem .2rem 1rem;
  border-radius: 10px;
}
</style>
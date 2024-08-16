<script setup lang="ts">
const supabase = useSupabaseClient()
const router = useRouter()
const route = useRoute()
let question_array = ref([])
const selected_answers = ref({})
const loading = ref(false)
const title = ref('')
const total_score = ref(0)
definePageMeta({
  layout: 'question'
})
const group_id = route.query.group
onMounted(() => {
  getQuestionById()
})
const getQuestionById = async () => {
  try {
    let {data: group, error: errorGroup} = await supabase
        .from('group')
        .select('*')
        .eq('id', group_id)
        .single()
    if (errorGroup) throw errorGroup
    title.value = group?.title

    let {data, error} = await supabase
        .from('question')
        .select('*')
        .eq('group_id', group_id)

    if (error) throw error
    question_array.value = data
  } catch (err) {
    throw err
  }
}
const questionOptions = (question: object) => {
  const answer_1 = {text: question.answer_1, value: 1}
  const answer_2 = {text: question.answer_2, value: 2}
  const answer_3 = {text: question.answer_3, value: 3}
  const answer_4 = {text: question.answer_4, value: 4}
  return [answer_1, answer_2, answer_3, answer_4].filter(option => option !== null)
}

const sendQuestion = async () => {
  const user = await supabase.auth.getUser()
  total_score.value = 0
  loading.value = true
  for (const question of question_array.value) {
    const user_answer = selected_answers.value[question.id]
    const is_correct = user_answer === question.correct_answer
    const score = is_correct ? 1 : 0
    total_score.value += score

    await supabase.from('user_answer').upsert({
      user_id: user.data.user.id,
      group_id: group_id,
      question_id: question.id,
      answer: selected_answers.value[question.id],
      is_correct: is_correct
    })
  }
  await supabase.from('user_score').upsert({
    user_id: user.data.user.id,
    group_id: group_id,
    score: total_score.value
  })

  loading.value = false
  router.push(`/question/result?group=${group_id}`).catch(() => {})

}
const cancelStartQuestion = () =>  {
  router.push(`/question`).catch(() => {})
}
const checkIsData = () => {
  return Object.keys(selected_answers).length === 0
}
</script>

<template>
  <div class="p-4">
    <div class="card">
      <div class="card-body">
        <div class="p-3">
          <div class="mb-2">{{ title }}</div>
          <hr/>
          <div class="mt-3">
            <div class="ml-5 mr-5" v-for="(quiz, index) in question_array" :key="index">
              <div>{{ index + 1 }}. {{ quiz.question }}</div>
              <div class="pl-5 mt-2 mb-2">
                <div v-for="(option, index) in questionOptions(quiz)" :key="index" class="flex items-center mb-2">
                  <input
                      type="radio"
                      :id="`q${quiz.id}-a${index}`"
                      :value="option.value"
                      v-model="selected_answers[quiz.id]"
                      :name="`question-${quiz.id}`"
                      class="mr-2"
                  />
                  <label :for="`q${quiz.id}-a${index}`" class="text-gray-700">{{ option.text }}</label>
                </div>
              </div>
            </div>
          </div>
          <div class="text-center mt-5">
            <button type="button" class="btn btn-secondary btn-sm mr-1 " @click="cancelStartQuestion" :disabled="loading">
              ยกเลิก
            </button>
            <button type="button" class="btn btn-primary btn-sm ml-1" @click="sendQuestion" :disabled="loading">
              ส่งคำตอบ
            </button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>

</style>
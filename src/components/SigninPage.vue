<script setup>
import { ref, watch } from 'vue'
import { useRouter } from 'vue-router'
import axios from 'axios'

const router = useRouter()
const apiUrl = 'https://todolist-api.hexschool.io'
const email = ref('')
const password = ref('')
const token = ref('')
const errors = ref({
  email: '',
  password: '',
})

const emit = defineEmits(['change-home-status'])
const changeHomeStatus = () => {
  emit('change-home-status')
}

watch(email, (newValue) => {
  const emailRegex = /^[a-zA-Z0-9._-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$/
  if (!newValue) {
    errors.value.email = `此欄位不可留空`
  } else if (!emailRegex.test(newValue)) {
    errors.value.email = `請輸入有效 email`
  } else {
    errors.value.email = ''
  }
})
watch(password, (newValue) => {
  if (newValue.length < 6) {
    errors.value.password = `長度不足 6 個字`
  } else {
    errors.value.password = ''
  }
})
//登入
const signin = async () => {
  try {
    const res = await axios.post(`${apiUrl}/users/sign_in`, {
      email: email.value,
      password: password.value,
    })
    token.value = res.data.token
    document.cookie = `customTodoToken=${res.data.token};path=;`
    alert(`登入成功`)
    router.push('/mylist')
  } catch (error) {
    alert(`${error.response.data.message}`)
    router.push('/')
  }
}
</script>
<template>
  <div>
    <form class="formControls" action="index.html">
      <h2 class="formControls_txt">最實用的線上代辦事項服務</h2>
      <label class="formControls_label" for="email">Email</label>
      <input
        class="formControls_input"
        type="text"
        id="email"
        name="email"
        placeholder="請輸入 email"
        required
        v-model="email"
      />
      <span v-if="errors.email">{{ errors.email }}</span>
      <label class="formControls_label" for="pwd">密碼</label>
      <input
        class="formControls_input"
        type="password"
        name="pwd"
        id="pwd"
        placeholder="請輸入密碼"
        required
        v-model="password"
      />
      <span v-if="errors.password">{{ errors.password }}</span>
      <input
        class="formControls_btnSubmit"
        type="button"
        onclick="javascript:location.href='#todoListPage'"
        value="登入"
        @click="signin"
      />
      <a href="" @click.prevent="changeHomeStatus" class="formControls_btnLink">註冊帳號</a>
    </form>
  </div>
</template>

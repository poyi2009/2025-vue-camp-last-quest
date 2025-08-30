<script setup>
import { ref, watch } from 'vue'
import axios from 'axios'

const apiUrl = 'https://todolist-api.hexschool.io'
const email = ref('')
const nickname = ref('')
const password = ref('')
const comparePassword = ref('')
const errors = ref({
  email: '',
  nickname: '',
  password: '',
  comparePassword: '',
})

const emit = defineEmits(['change-home-status'])
const changeHomeStatus = () => {
  emit('change-home-status')
}

watch(email, (newValue) => {
  const emailRegex = /^[a-zA-Z0-9._-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$/
  if (!emailRegex.test(newValue)) {
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
watch(nickname, (newValue) => {
  if (newValue.length < 1) {
    errors.value.nickname = `至少輸入 1 個字元`
  } else {
    errors.value.nickname = ''
  }
})
watch(comparePassword, (newValue) => {
  if (newValue !== password.value) {
    errors.value.comparePassword = `與密碼設定不相符`
  } else {
    errors.value.comparePassword = ''
  }
})

const signup = async () => {
  try {
    if (comparePassword.value !== password.value) {
      return
    }
    await axios.post(`${apiUrl}/users/sign_up`, {
      email: email.value,
      nickname: nickname.value,
      password: password.value,
    })
    alert(`註冊成功`)
    email.value = ''
    nickname.value = ''
    password.value = ''
    comparePassword.value = ''
    changeHomeStatus()
  } catch (error) {
    alert(`${error.response.data.message}`)
  }
}
</script>
<template>
  <div>
    <form class="formControls" action="index.html">
      <h2 class="formControls_txt">註冊帳號</h2>
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
      <label class="formControls_label" for="name">您的暱稱</label>
      <input
        class="formControls_input"
        type="text"
        name="name"
        id="name"
        placeholder="請輸入您的暱稱"
        v-model="nickname"
      />
      <span v-if="errors.nickname">{{ errors.nickname }}</span>
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
      <label class="formControls_label" for="pwd">再次輸入密碼</label>
      <input
        class="formControls_input"
        type="password"
        name="pwd"
        id="pwd"
        placeholder="請再次輸入密碼"
        required
        v-model="comparePassword"
      />
      <span v-if="errors.comparePassword">{{ errors.comparePassword }}</span>
      <input class="formControls_btnSubmit" type="button" value="註冊帳號" @click="signup" />
      <a href="" @click.prevent="changeHomeStatus" class="formControls_btnLink">登入</a>
    </form>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import SigninPage from '@/components/SigninPage.vue'
import SignupPage from '@/components/SignupPage.vue'
const homeStatus = ref(true)
const changeHomeStatus = () => {
  homeStatus.value = !homeStatus.value
}
</script>

<template>
  <div id="loginPage" class="bg-yellow">
    <div class="conatiner loginPage vhContainer">
      <div class="side">
        <a href="#"
          ><img
            class="logoImg"
            src="https://raw.githubusercontent.com/hexschool/2022-web-layout-training/main/todolist/logo.png"
            alt=""
        /></a>
        <img
          class="d-m-n"
          src="https://raw.githubusercontent.com/hexschool/2022-web-layout-training/main/todolist/img.png"
          alt="workImg"
        />
      </div>
      <div v-if="homeStatus">
        <SigninPage @change-home-status="changeHomeStatus" />
      </div>
      <div v-else>
        <SignupPage :homeStatus="homeStatus" @change-home-status="changeHomeStatus" />
      </div>
    </div>
  </div>
</template>

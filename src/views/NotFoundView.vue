<script setup>
import { useRouter } from 'vue-router'
const router = useRouter()
setTimeout(() => {
  router.push('/')
}, 3000)
</script>
<template>
  <div id="" class="bg-yellow">
    <div class="conatiner vhContainer">
      <div style="text-align: center">
        <p style="font-size: 25px; font-weight: 200; padding: 100px">404 此頁面不存在</p>
        <p>將於 5 秒後回到首頁</p>
      </div>
    </div>
  </div>
</template>

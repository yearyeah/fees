<script setup lang="ts">
import IB from './components/IB.vue'
import Changqiao from './components/Changqiao.vue'

import { ref } from 'vue'

const transactionAmount = ref(27080)


</script>

<template>
  <h2>交易费用计算器</h2>
  <div>交易金额：<input type="number" v-model="transactionAmount" /></div>
  <IB :transactionAmount='transactionAmount' /> 
  <Changqiao :transactionAmount = "transactionAmount"></Changqiao>
</template>

<style scoped>
.logo {
  height: 6em;
  padding: 1.5em;
  will-change: filter;
  transition: filter 300ms;
}
.logo:hover {
  filter: drop-shadow(0 0 2em #646cffaa);
}
.logo.vue:hover {
  filter: drop-shadow(0 0 2em #42b883aa);
}
</style>

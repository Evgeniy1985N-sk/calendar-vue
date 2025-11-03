<script setup>
import { ref } from 'vue'
import Calendar from './components/Calendar.vue'

const dateCurrent = ref('')

function getDate(date) {
  const arr = date.split('-')
  arr[1] = ++arr[1]
  dateCurrent.value = arr.join('-')
}
</script>

<template>
  <div class="wrapper">
    <Calendar @get-date="getDate" date="1985-8-7" />
    <p class="date">
      {{ dateCurrent }}
    </p>
  </div>
</template>

<style scoped>
.wrapper {
  display: flex;
  gap: 1rem;
}

.date {
  display: flex;
  align-items: center;
  justify-content: center;
  height: 56px;
  min-width: 160px;
  margin: 0;
}
</style>

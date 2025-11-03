<script setup>
import { onMounted, reactive, ref, watch, } from 'vue'

const dateCurrent = new Date()
const year = ref('')
const month = ref('')
const days = ref([])
const date = reactive({
  year: dateCurrent.getFullYear(),
  month: dateCurrent.getMonth(),
  day: dateCurrent.getDate()
})

const props = defineProps(['date'])

onMounted(() => {
  if (props.date) setDate()
  year.value = dateCurrent.getFullYear()
  month.value = dateCurrent.toLocaleString('en-US', { month: 'short' });
  weekDays()
})

function prevMonth() {
  dateCurrent.setMonth(dateCurrent.getMonth() - 1);
  month.value = dateCurrent.toLocaleString('en-US', { month: 'short' });
}

function nextMonth() {
  dateCurrent.setMonth(dateCurrent.getMonth() + 1);
  month.value = dateCurrent.toLocaleString('en-US', { month: 'short' });
}

watch(month, (newMonth, oldMonth) => {
  if (newMonth === 'Dec' && oldMonth === 'Jan') {
    year.value = dateCurrent.getFullYear()
  }

  if (newMonth === 'Jan' && oldMonth === 'Dec') {
    year.value = dateCurrent.getFullYear()
  }

  weekDays()
})

function allDays() {
  return new Date(dateCurrent.getFullYear(), dateCurrent.getMonth() + 1, 0).getDate();
}

function weekDays() {
  days.value = []
  for (let i = 1; i <= allDays(); i++) {
    let day = new Date(dateCurrent.getFullYear(), dateCurrent.getMonth(), i).getDay();
    days.value.push({
      number: i,
      week: day,
      isActive: false,
    })
  }
  if (dateCurrent.getFullYear() === date.year && dateCurrent.getMonth() === date.month) {
    toggleActive(date.day - 1)
  } else {
    // toggleActive(null)
  }
}

function getDate(day, index) {
  date.value = year.value + ' ' + month.value + ' ' + day.number;

  dateCurrent.setDate(day.number)

  toggleActive(index)
}

function setDate(y, m, d) {
  const arr = props.date.split('-')
  y = arr[0]
  m = arr[1]
  d = arr[2]

  dateCurrent.setFullYear(y)
  dateCurrent.setMonth(m - 1)
  dateCurrent.setDate(d)

  date.year = dateCurrent.getFullYear()
  date.month = dateCurrent.getMonth()
  date.day = dateCurrent.getDate()

}

function toggleActive(index) {
  date.year = dateCurrent.getFullYear()
  date.month = dateCurrent.getMonth()
  date.day = index + 1
  days.value = days.value.map((d) => {
    if (d.isActive) {
      d.isActive = false
    }
    return d
  })
  Object.assign(days.value[index], {
    isActive: true
  })
}

</script>

<template>
  <h1>
    Calendar
  </h1>

  <div class="wrapper">


    <div class="calendar">

      <div class="months">
        <div @click="prevMonth" class="arrow">
          <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5"
            stroke="currentColor" class="size-6">
            <path stroke-linecap="round" stroke-linejoin="round" d="M6.75 15.75 3 12m0 0 3.75-3.75M3 12h18" />
          </svg>
        </div>
        <p>
          {{ month }} {{ year }}
        </p>
        <div @click="nextMonth" class="arrow arrow_right">
          <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5"
            stroke="currentColor" class="size-6">
            <path stroke-linecap="round" stroke-linejoin="round" d="M17.25 8.25 21 12m0 0-3.75 3.75M21 12H3" />
          </svg>

        </div>
      </div>

      <div class="week">
        <span>Sun</span>
        <span>Mon</span>
        <span>Tue</span>
        <span>Wed</span>
        <span>Thu</span>
        <span>Fri</span>
        <span>Sut</span>
      </div>

      <div class="days">
        <div @click="getDate(day, index)" v-for="(day, index) in days" :data-col="day.week"
          :class="[day.isActive ? 'day active' : 'day']">
          <span v-if="day.week === 0">
            {{ day.number }}
          </span>
          <span v-if="day.week === 1">
            {{ day.number }}
          </span>
          <span v-if="day.week === 2">
            {{ day.number }}
          </span>
          <span v-if="day.week === 3">
            {{ day.number }}
          </span>
          <span v-if="day.week === 4">
            {{ day.number }}
          </span>
          <span v-if="day.week === 5">
            {{ day.number }}
          </span>
          <span v-if="day.week === 6">
            {{ day.number }}
          </span>
        </div>
      </div>

    </div>

    <p class="date">
      {{ date.year }}-{{ date.month + 1 }}-{{ date.day }}
    </p>

  </div>

</template>

<style scoped>
.wrapper {
  display: flex;
  gap: 1rem;
}

.months {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 1rem;
}

.arrow {
  display: flex;
  justify-content: center;
  align-items: center;
  width: 22px;
  height: 22px;
  cursor: pointer;
}

.date {
  display: flex;
  align-items: center;
  justify-content: center;
  height: 56px;
  min-width: 160px;
  margin: 0;
}

.week {
  display: grid;
  grid-template-columns: repeat(7, 1fr);
  gap: .8rem;
  padding: 1rem 0;
}

.days {
  display: grid;
  grid-template-columns: repeat(7, 1fr);
  gap: .8rem;
}

.day.active {
  border-color: rgb(130, 103, 194);
}

.day {
  width: 34px;
  height: 34px;
  display: flex;
  align-items: center;
  justify-content: center;
  border: 1px solid transparent;
  cursor: pointer;
}

[data-col="0"] {
  grid-column: 1;
}

[data-col="1"] {
  grid-column: 2;
}

[data-col="2"] {
  grid-column: 3;
}

[data-col="3"] {
  grid-column: 4;
}

[data-col="4"] {
  grid-column: 5;
}

[data-col="5"] {
  grid-column: 6;
}

[data-col="6"] {
  grid-column: 7;
}
</style>

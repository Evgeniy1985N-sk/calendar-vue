<script setup>
import { onMounted, reactive, ref, watch, } from 'vue'

const dateCurrent = ref(new Date())
const year = ref('')
const month = ref('')
const days = ref([])
const date = reactive({
  year: dateCurrent.value.getFullYear(),
  month: dateCurrent.value.getMonth(),
  day: dateCurrent.value.getDate()
})
const lang = ref('En')
const weekDaysEn = ref(['Sun', 'Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat'])
const weekDaysRu = ref(['Вс', 'Пн', 'Вт', 'Ср', 'Чт', 'Пт', 'Сб'])

const props = defineProps(['date'])

onMounted(() => {
  if (props.date) setDate()
  year.value = dateCurrent.value.getFullYear()
  changeMonth()
  weekDays()
})

function prevMonth() {
  dateCurrent.value.setMonth(dateCurrent.value.getMonth() - 1);
  changeMonth()
}

function nextMonth() {
  dateCurrent.value.setMonth(dateCurrent.value.getMonth() + 1);
  changeMonth()
}

watch(month, (newMonth, oldMonth) => {
  if (newMonth === 'Dec' && oldMonth === 'Jan' || newMonth === 'дек.' && oldMonth === 'янв.') {
    year.value = dateCurrent.value.getFullYear()
  }

  if (newMonth === 'Jan' && oldMonth === 'Dec' || newMonth === 'янв.' && oldMonth === 'дек.') {
    year.value = dateCurrent.value.getFullYear()
  }

  weekDays()
})

function allDays() {
  return new Date(dateCurrent.value.getFullYear(), dateCurrent.value.getMonth() + 1, 0).getDate();
}

function weekDays() {
  days.value = []
  for (let i = 1; i <= allDays(); i++) {
    let day = new Date(dateCurrent.value.getFullYear(), dateCurrent.value.getMonth(), i).getDay();
    days.value.push({
      number: i,
      week: day,
      isActive: false,
    })
  }
  if (dateCurrent.value.getFullYear() === date.year && dateCurrent.value.getMonth() === date.month) {
    toggleActive(date.day - 1)
  }
}

function getDate(day, index) {

  dateCurrent.value.setDate(day.number)

  toggleActive(index)
}

function setDate(y, m, d) {
  const arr = props.date.split('-')
  y = arr[0]
  m = arr[1]
  d = arr[2]

  dateCurrent.value.setFullYear(y)
  dateCurrent.value.setMonth(m - 1)
  dateCurrent.value.setDate(d)

  date.year = dateCurrent.value.getFullYear()
  date.month = dateCurrent.value.getMonth()
  date.day = dateCurrent.value.getDate()
}

function toggleActive(index) {
  date.year = dateCurrent.value.getFullYear()
  date.month = dateCurrent.value.getMonth()
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

function toggleLangButton() {
  lang.value = lang.value === 'En' ? 'Ru' : 'En'
}

watch(lang, changeMonth)

function changeMonth() {
  return month.value = dateCurrent.value.toLocaleString(lang.value === 'En' ? 'En' : 'Ru', { month: 'short' });
}

</script>

<template>
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

      <div v-if="lang === 'En'" class="week">
        <span v-for="day in weekDaysEn">
          {{ day }}
        </span>
      </div>

      <div v-if="lang === 'Ru'" class="week">
        <span v-for="day in weekDaysRu">
          {{ day }}
        </span>
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

      <button @click="toggleLangButton">{{ lang }}</button>

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

.date {
  display: flex;
  align-items: center;
  justify-content: center;
  height: 56px;
  min-width: 160px;
  margin: 0;
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
  margin-bottom: 1rem;
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

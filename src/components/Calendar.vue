<script setup>
import { onMounted, reactive, ref, watch, watchEffect, } from 'vue'

const dateCurrent = new Date()
const year = ref('')
const month = ref('')
const days = ref([])
const date = reactive({
  year: dateCurrent.getFullYear(),
  month: dateCurrent.getMonth(),
  day: dateCurrent.getDate()
})
const lang = ref('En')
const weekDaysEn = ref(['Sun', 'Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sut'])
const weekDaysRu = ref(['Вс', 'Пн', 'Вт', 'Ср', 'Чт', 'Пт', 'Сб'])

const props = defineProps(['date'])
const emit = defineEmits(['getDate', 'date'])

onMounted(() => {
  if (props.date) setDate()
  year.value = dateCurrent.getFullYear()
  if (lang.value === 'En') {
    month.value = dateCurrent.toLocaleString('en-US', { month: 'short' });
  }
  if (lang.value === 'Ru') {
    month.value = dateCurrent.toLocaleString('ru-RU', { month: 'short' });
  }
  weekDays()
})

function prevMonth() {
  dateCurrent.setMonth(dateCurrent.getMonth() - 1);
  if (lang.value === 'En') {
    month.value = dateCurrent.toLocaleString('en-US', { month: 'short' });
  }
  if (lang.value === 'Ru') {
    month.value = dateCurrent.toLocaleString('ru-RU', { month: 'short' });
  }
}

function nextMonth() {
  dateCurrent.setMonth(dateCurrent.getMonth() + 1);
  if (lang.value === 'En') {
    month.value = dateCurrent.toLocaleString('en-US', { month: 'short' });
  }
  if (lang.value === 'Ru') {
    month.value = dateCurrent.toLocaleString('ru-RU', { month: 'short' });
  }
}

watch(month, (newMonth, oldMonth) => {
  if (newMonth === 'Dec' && oldMonth === 'Jan' || newMonth === 'дек.' && oldMonth === 'янв.') {
    year.value = dateCurrent.getFullYear()
  }

  if (newMonth === 'Jan' && oldMonth === 'Dec' || newMonth === 'янв.' && oldMonth === 'дек.') {
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
  }
}

function getDate(day, index) {

  dateCurrent.setDate(day.number)

  toggleActive(index)

  emit('getDate', date.year + '-' + dateCurrent.getMonth() + '-' + date.day)

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

function toggleLang() {
  lang.value = lang.value === 'En' ? 'Ru' : 'En'
}

watch(lang, (newLang) => {
  if (newLang === 'En') {
    month.value = dateCurrent.toLocaleString('en-US', { month: 'short' });
  }
  if (newLang === 'Ru') {
    month.value = dateCurrent.toLocaleString('ru-RU', { month: 'short' });
  }
})

</script>

<template>

  <div class="calendar">

    <div class="months">
      <div @click="prevMonth" class="arrow">
        <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor"
          class="size-6">
          <path stroke-linecap="round" stroke-linejoin="round" d="M6.75 15.75 3 12m0 0 3.75-3.75M3 12h18" />
        </svg>
      </div>
      <p>
        {{ month }} {{ year }}
      </p>
      <div @click="nextMonth" class="arrow arrow_right">
        <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor"
          class="size-6">
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

    <button @click="toggleLang">{{ lang }}</button>

  </div>

</template>

<style scoped>
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

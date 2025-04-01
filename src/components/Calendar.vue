<template>
  <div class="p-4 bg-white rounded-lg shadow">
    <div class="flex justify-between items-center mb-4">
      <button 
        @click="previousMonth"
        class="p-2 hover:bg-gray-100 rounded-full"
      >
        <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15 19l-7-7 7-7" />
        </svg>
      </button>
      <h2 class="text-xl font-semibold text-gray-800">
        {{ currentMonth }} {{ currentYear }}
      </h2>
      <button 
        @click="nextMonth"
        class="p-2 hover:bg-gray-100 rounded-full"
      >
        <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 5l7 7-7 7" />
        </svg>
      </button>
    </div>

    <div class="grid grid-cols-7 gap-1 mb-2">
      <div v-for="day in ['Sun', 'Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat']" 
           class="text-center text-sm font-medium text-gray-600 p-2">
        {{ day }}
      </div>
    </div>

    <div class="grid grid-cols-7 gap-1">
      <div v-for="day in days" 
           :key="day"
           @click="selectDate(day)"
           class="aspect-square flex items-center justify-center cursor-pointer relative">
        <div v-if="day" 
             :class="[
               'w-10 h-10 flex items-center justify-center rounded-full transition-colors',
               isSelectedDate(day) ? 'bg-blue-500 text-white' : 'hover:bg-gray-100',
               isToday(day) ? 'ring-2 ring-blue-500' : ''
             ]"
        >
          {{ day }}
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'

const emit = defineEmits(['dateSelected'])
const currentDate = ref(new Date())
const selectedDate = ref(new Date())

const months = [
  'January', 'February', 'March', 'April', 'May', 'June',
  'July', 'August', 'September', 'October', 'November', 'December'
]

const currentMonth = computed(() => months[currentDate.value.getMonth()])
const currentYear = computed(() => currentDate.value.getFullYear())

const daysInMonth = computed(() => {
  const year = currentDate.value.getFullYear()
  const month = currentDate.value.getMonth()
  return new Date(year, month + 1, 0).getDate()
})

const firstDayOfMonth = computed(() => {
  const year = currentDate.value.getFullYear()
  const month = currentDate.value.getMonth()
  return new Date(year, month, 1).getDay()
})

const days = computed(() => {
  const days = []
  const totalDays = daysInMonth.value
  const firstDay = firstDayOfMonth.value

  // Add empty spaces for days before the first day of the month
  for (let i = 0; i < firstDay; i++) {
    days.push(null)
  }

  // Add the days of the month
  for (let i = 1; i <= totalDays; i++) {
    days.push(i)
  }

  return days
})

const previousMonth = () => {
  currentDate.value = new Date(
    currentDate.value.getFullYear(),
    currentDate.value.getMonth() - 1,
    1
  )
}

const nextMonth = () => {
  currentDate.value = new Date(
    currentDate.value.getFullYear(),
    currentDate.value.getMonth() + 1,
    1
  )
}

const selectDate = (day) => {
  if (day) {
    selectedDate.value = new Date(
      currentDate.value.getFullYear(),
      currentDate.value.getMonth(),
      day
    )
    emit('dateSelected', selectedDate.value)
  }
}

const isSelectedDate = (day) => {
  if (!day) return false
  const date = new Date(
    currentDate.value.getFullYear(),
    currentDate.value.getMonth(),
    day
  )
  return date.toDateString() === selectedDate.value.toDateString()
}

const isToday = (day) => {
  if (!day) return false
  const today = new Date()
  return (
    day === today.getDate() &&
    currentDate.value.getMonth() === today.getMonth() &&
    currentDate.value.getFullYear() === today.getFullYear()
  )
}
</script>

<style scoped>
  div:hover {
    background-color: #93c5fd;
  }
</style>
<template>
  <div class="p-4 bg-white rounded-lg shadow" dir="rtl">
    <div class="flex justify-between items-center mb-4">
      <button 
        @click="nextMonth"
        class="p-2 hover:bg-gray-100 rounded-lg transition-colors duration-200 flex items-center"
        aria-label="الشهر التالي"
      >
        <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" viewBox="0 0 20 20" fill="currentColor">
          <path fill-rule="evenodd" d="M12.707 5.293a1 1 0 010 1.414L9.414 10l3.293 3.293a1 1 0 01-1.414 1.414l-4-4a1 1 0 010-1.414l4-4a1 1 0 011.414 0z" clip-rule="evenodd" />
        </svg>
        <span class="mr-1 text-sm">الشهر السابق</span>
      </button>
      <h2 class="text-xl font-semibold text-gray-800">
        {{ currentMonth }} {{ currentYear }}
      </h2>
      <button 
        @click="previousMonth"
        class="p-2 hover:bg-gray-100 rounded-lg transition-colors duration-200 flex items-center"
        aria-label="الشهر السابق"
      >
        <span class="ml-1 text-sm">الشهر التالي</span>
        <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" viewBox="0 0 20 20" fill="currentColor">
          <path fill-rule="evenodd" d="M7.293 14.707a1 1 0 010-1.414L10.586 10 7.293 6.707a1 1 0 011.414-1.414l4 4a1 1 0 010 1.414l-4 4a1 1 0 01-1.414 0z" clip-rule="evenodd" />
        </svg>
      </button>
    </div>

    <div class="grid grid-cols-7 gap-1 mb-2">
      <div v-for="day in weekDays" 
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
  'يناير', 'فبراير', 'مارس', 'أبريل', 'مايو', 'يونيو',
  'يوليو', 'أغسطس', 'سبتمبر', 'أكتوبر', 'نوفمبر', 'ديسمبر'
]

const weekDays = ['الأحد', 'الإثنين', 'الثلاثاء', 'الأربعاء', 'الخميس', 'الجمعة', 'السبت']

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
.calendar-nav-button {
  @apply flex items-center px-3 py-2 rounded-lg text-gray-700 hover:bg-gray-100 transition-colors duration-200;
}

.calendar-nav-button:hover {
  @apply bg-gray-100 text-gray-900;
}
</style>
<script setup>
import { ref } from 'vue'
import Calendar from './components/Calendar.vue'
import HabitList from './components/HabitList.vue'
import AppDescription from './components/AppDescription.vue'

const selectedDate = ref(new Date())

const handleDateSelected = (date) => {
  selectedDate.value = date
}
</script>

<template>
  <header>
    <div class="container mx-auto p-4" dir="rtl">
      <div class="grid grid-cols-1 md:grid-cols-3 gap-6">
        <div class="md:col-span-1">
          <Calendar @dateSelected="handleDateSelected" />
          <AppDescription />
        </div>
        <div class="md:col-span-2">
          <HabitList :selectedDate="selectedDate" />
        </div>
      </div>
    </div>
  </header>
</template>

<style>
@import url('https://fonts.googleapis.com/css2?family=Noto+Naskh+Arabic:wght@400;500;600;700&display=swap');

:root {
  direction: rtl;
}

body {
  font-family: 'Noto Naskh Arabic', serif;
  background-color: #f3f4f6;
}
</style>
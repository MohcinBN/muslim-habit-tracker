<script setup>
import { ref } from 'vue'
import Calendar from './components/Calendar.vue'
import HabitList from './components/HabitList.vue'

const selectedDate = ref(new Date())

const handleDateSelected = (date) => {
  selectedDate.value = date
}
</script>

<template>
  <header>
    <div class="container mx-auto p-4">
      <div class="grid grid-cols-1 md:grid-cols-3 gap-6">
        <div class="md:col-span-1">
          <Calendar @dateSelected="handleDateSelected" />
        </div>
        <div class="md:col-span-2">
          <HabitList :selectedDate="selectedDate" />
        </div>
      </div>
    </div>
  </header>
</template>

<style scoped>

</style>
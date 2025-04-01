<script setup>
import { ref, onMounted, watch } from 'vue'

const props = defineProps({
  selectedDate: {
    type: Date,
    required: true
  }
})

const habits = ref([])
const customHabits = ref([])

const defaultHabits = [
  {
    id: 1,
    text: "قراءة صفحة من القرآن يومياً",
    checked: false
  },
  {
    id: 2,
    text: "تعلم في مجال تخصصك",
    checked: false
  },
  {
    id: 3,
    text: "الصلاة على وقتها",
    checked: false
  },
  {
    id: 4,
    text: "التمرين لمدة 30 دقيقة على الأقل",
    checked: false
  },
  {
    id: 5,
    text: "أذكار الصباح والمساء",
    checked: false
  }
]

const addHabit = (text) => {
  customHabits.value.push({
    id: Date.now(),
    text: text,
    checked: false
  })
  saveToLocalStorage()
}

const toggleHabit = (id, isDefault = false) => {
  const list = isDefault ? habits.value : customHabits.value
  const habit = list.find(h => h.id === id)
  if (habit) {
    habit.checked = !habit.checked
    saveToLocalStorage()
  }
}

const getDateKey = (date) => {
  return date.toISOString().split('T')[0]
}

const saveToLocalStorage = () => {
  const dateKey = getDateKey(props.selectedDate)
  const habitData = {
    defaultHabits: habits.value,
    customHabits: customHabits.value
  }
  const allHabits = JSON.parse(localStorage.getItem('all_habits') || '{}')
  allHabits[dateKey] = habitData
  localStorage.setItem('all_habits', JSON.stringify(allHabits))
}

const loadFromLocalStorage = () => {
  const dateKey = getDateKey(props.selectedDate)
  const allHabits = JSON.parse(localStorage.getItem('all_habits') || '{}')
  const dateHabits = allHabits[dateKey]

  if (dateHabits) {
    habits.value = dateHabits.defaultHabits
    customHabits.value = dateHabits.customHabits
  } else {
    habits.value = defaultHabits.map(habit => ({ ...habit, checked: false }))
    customHabits.value = []
    saveToLocalStorage()
  }
}

const formatDate = (date) => {
  return new Intl.DateTimeFormat('ar-SA', {
    weekday: 'long',
    year: 'numeric',
    month: 'long',
    day: 'numeric'
  }).format(date)
}

watch(() => props.selectedDate, () => {
  loadFromLocalStorage()
})

onMounted(() => {
  loadFromLocalStorage()
})

watch([habits, customHabits], () => {
  saveToLocalStorage()
}, { deep: true })
</script>

<template>
  <div class="p-4">
    <div class="mb-4">
      <h2 class="text-2xl font-bold text-gray-800">العادات ليوم {{ formatDate(props.selectedDate) }}</h2>
    </div>
    
    <!-- Default Habits Section -->
    <div class="mb-6">
      <h3 class="text-lg font-semibold mb-3 text-gray-700">العادات الأساسية</h3>
      <ul class="space-y-2">
        <li v-for="habit in habits" :key="habit.id" 
            class="flex items-center justify-between p-3 bg-white rounded-lg shadow hover:shadow-md transition-shadow duration-200">
          <div class="flex items-center space-x-3 space-x-reverse">
            <input
              type="checkbox"
              :checked="habit.checked"
              @change="toggleHabit(habit.id, true)"
              class="form-checkbox h-5 w-5 text-blue-600 rounded border-gray-300 focus:ring-blue-500"
            >
            <span 
              :class="{ 
                'line-through text-gray-400': habit.checked,
                'text-gray-800': !habit.checked
              }"
              class="text-sm font-medium"
            >{{ habit.text }}</span>
          </div>
        </li>
      </ul>
    </div>

    <!-- Custom Habits Section -->
    <div class="mt-8">
      <h3 class="text-lg font-semibold mb-3 text-gray-700">عادات إضافية</h3>
      <div class="mb-4">
        <input
          type="text"
          @keyup.enter="$event.target.value && addHabit($event.target.value) && ($event.target.value = '')"
          placeholder="أضف عادة جديدة..."
          class="w-full p-2 border rounded focus:outline-none focus:ring-2 focus:ring-blue-500 text-right"
        >
      </div>
      <ul class="space-y-2">
        <li v-for="habit in customHabits" :key="habit.id" 
            class="flex items-center justify-between p-3 bg-white rounded-lg shadow hover:shadow-md transition-shadow duration-200">
          <div class="flex items-center space-x-3 space-x-reverse">
            <input
              type="checkbox"
              :checked="habit.checked"
              @change="toggleHabit(habit.id, false)"
              class="form-checkbox h-5 w-5 text-blue-600 rounded border-gray-300 focus:ring-blue-500"
            >
            <span 
              :class="{ 
                'line-through text-gray-400': habit.checked,
                'text-gray-800': !habit.checked
              }"
              class="text-sm font-medium"
            >{{ habit.text }}</span>
          </div>
        </li>
      </ul>
    </div>
  </div>
</template>
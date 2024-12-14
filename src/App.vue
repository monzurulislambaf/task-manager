<script setup>
import { ref, onMounted, watch } from 'vue';

const tasks = ref([]);
const showForm = ref(false);
const newTask = ref('');

onMounted(() => {
  const savedTasks = localStorage.getItem('tasks');
  tasks.value = savedTasks ? JSON.parse(savedTasks) : [];
});

watch(
  tasks,
  (newVal) => {
    localStorage.setItem('tasks', JSON.stringify(newVal));
  },
  { deep: true }
);

const toggleForm = () => {
  showForm.value = !showForm.value;
};

const addTask = () => {
  if (newTask.value.trim().length < 3) {
    alert('Task name must be at least 3 characters.');
    return;
  }
  tasks.value.push({ name: newTask.value.trim(), completed: false });
  newTask.value = '';
};

const deleteTask = (index) => {
  tasks.value.splice(index, 1);
};

const toggleCompletion = (index) => {
  tasks.value[index].completed = !tasks.value[index].completed;
};

const taskStyle = (completed) =>
  completed
    ? 'bg-green-100 border-red-500 border-2'
    : 'bg-white border-gray-300 border';
</script>

<template>
  <div class="max-w-lg mx-auto p-4">
    <h1 class="text-2xl font-bold mb-4 text-center">Task Manager</h1>

    <button
      @click="toggleForm"
      class="btn btn-primary mb-4 w-full"
    >
      {{ showForm ? 'Close Form' : 'Add Task' }}
    </button>

    <div v-if="showForm" class="mb-4 space-y-2">
      <input
        v-model="newTask"
        type="text"
        placeholder="Enter task name"
        class="input input-bordered w-full"
      />
      <button @click="addTask" class="btn btn-success w-full">Add Task</button>
    </div>

    <p v-if="!tasks.length" class="text-center text-gray-500">No tasks available</p>

    <ul class="space-y-3">
      <li
        v-for="(task, index) in tasks"
        :key="index"
        :class="`p-4 rounded-md shadow ${taskStyle(task.completed)}`"
      >
        <div class="flex items-center justify-between">
          <span
            @click="toggleCompletion(index)"
            class="cursor-pointer text-lg font-medium"
            :class="{ 'line-through': task.completed }"
          >
            {{ task.name }}
          </span>

          <div class="flex space-x-2">
            <button
              @click="toggleCompletion(index)"
              :class="task.completed ? 'btn btn-warning btn-sm' : 'btn btn-success btn-sm'"
            >
              {{ task.completed ? 'Undo' : 'Complete' }}
            </button>
            <button @click="deleteTask(index)" class="btn btn-error btn-sm">Delete</button>
          </div>
        </div>
      </li>
    </ul>
  </div>
</template>

<style scoped>
</style>

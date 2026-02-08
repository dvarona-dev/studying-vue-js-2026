<script setup>
import { ref } from 'vue'

const newTask = ref('')
const tasks = ref([])

function addTask() {
  const task = newTask.value.trim()
  if (!task) {
    return
  }

  tasks.value.push({
    id: Date.now(),
    description: task,
    isCompleted: false,
    isFavorite: false,
  })

  newTask.value = ''
}

function removeTask(id) {
  tasks.value = tasks.value.filter(({ id: taskId }) => taskId !== id)
}

const editingTaskId = ref(null)
const editingBuffer = ref('')

function startEdit(task) {
  editingTaskId.value = task.id
  editingBuffer.value = task.description
}

function cancelEdit() {
  editingTaskId.value = null
  editingBuffer.value = ''
}

function finishEdit(task) {
  if (editingTaskId.value !== task.id) return

  const trimmed = editingBuffer.value.trim()

  if (!trimmed) {
    removeTask(task.id)
  } else {
    task.description = trimmed
  }

  cancelEdit()
}

function favoriteTask(task) {
  task.isFavorite = !task.isFavorite
}
</script>

<template>
  <div class="wrapper">
    <h1>Todo App</h1>

    <div class="input-row">
      <input type="text" placeholder="Add task here..." v-model="newTask" />
      <button @click="addTask">Add</button>
    </div>

    <ul class="task-list">
      <li
        v-for="task in tasks"
        :key="task.id"
        :class="{ done: task.isCompleted, editing: editingTaskId === task.id }"
      >
        <template v-if="editingTaskId === task.id">
          <input
            type="text"
            v-model="editingBuffer"
            class="edit-input"
            @keyup.enter="finishEdit(task)"
            @keyup.esc="cancelEdit()"
            @blur="finishEdit(task)"
            :ref="(el) => el && el.focus()"
          />
        </template>

        <template v-else>
          <button class="deleteBtn" @click="removeTask(task.id)">X</button>
          <button class="favBtn" @click="favoriteTask(task)">
            {{ task.isFavorite ? '★' : '☆' }}
          </button>
          <input type="checkbox" v-model="task.isCompleted" />
          <span class="description" @click="startEdit(task)">{{ task.description }}</span>
        </template>
      </li>
    </ul>
  </div>
</template>

<style scoped>
.wrapper {
  max-width: 500px;
  margin: 2rem auto;
  font-family: sans-serif;
  text-align: center;
}

.input-row {
  display: flex;
  gap: 0.5rem;
  margin-bottom: 1rem;
}

.input-row input {
  flex-grow: 1;
  padding: 0.5rem;
  border-radius: 6px;
  border: 1px solid #ccc;
}

button {
  padding: 0.5rem 1rem;
  border-radius: 6px;
  border: 1px solid #ccc;
  cursor: pointer;
}

.task-list {
  list-style: none;
  padding: 0;
}

.task-list li {
  display: flex;
  align-items: start;
  gap: 0.5rem;
  padding: 0.5rem;
  border-bottom: 1px solid #eee;
}

.task-list li .description:hover {
  cursor: pointer;
}

.task-list li.done .description {
  text-decoration: line-through;
  opacity: 0.6;
}

.deleteBtn {
  background: #e53e3e;
  color: white;
  border: none;
  border-radius: 4px;
  padding: 0.2rem 0.5rem;
  cursor: pointer;
}

.deleteBtn:hover {
  background: #c53030;
}

.editing .edit-input {
  flex-grow: 1;
  padding: 0.5rem;
  border: 1px solid #333;
  border-radius: 6px;
  font-size: 1rem;
}

.favBtn {
  background: none;
  border: none;
  padding: 0;
  cursor: pointer;
  font-size: 1.2rem;
  color: #f6c90e;
}

.favBtn:hover {
  transform: scale(1.2);
}
</style>

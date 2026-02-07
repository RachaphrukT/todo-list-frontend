<script setup>
import { ref, onMounted } from 'vue'

const todos = ref([])
const newTitle = ref('')

// api
const fetchTodos = async () => {
  const res = await fetch('http://localhost:3000/api/todos')
  todos.value = await res.json()
}

onMounted(fetchTodos)


const addTodo = async () => {
  if (!newTitle.value.trim()) return

  await fetch('http://localhost:3000/api/todos', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ title: newTitle.value })
  })

  newTitle.value = ''
  fetchTodos()
}

// completed status
const toggleTodo = async (todo) => {
  await fetch(`http://localhost:3000/api/todos/${todo.id}`, {
    method: 'PUT',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({
      completed: todo.completed === 1 ? 0 : 1
    })
  })

  fetchTodos()
}

const deleteTodo = async (id) => {
  await fetch(`http://localhost:3000/api/todos/${id}`, {
    method: 'DELETE'
  })

  fetchTodos()
}
</script>

<template>
  <div class="page">
  <div class="container">
    <h1>Todo List</h1>

    <div class="add-box">
  <div class="inputBox">
    <input
  v-model="newTitle"
  type="text"
  placeholder=" "
/>

    <span>Add new task</span>
  </div>

  <button @click="addTodo">Add</button>
</div>


    <ul>
      <li class="todo-item" v-for="t in todos" :key="t.id">
        <label>
          <input
            type="checkbox"
            :checked="t.completed === 1"
            @change="toggleTodo(t)"
          />
          <span :class="{ done: t.completed === 1 }">
            {{ t.title }}
          </span>
        </label>

        <button class="delete" @click="deleteTodo(t.id)">Delete</button>
      </li>
    </ul>
  </div>
  </div>
</template>


<style>
html, body {
  height: auto;
}

#app{
  grid: none;
}

.container {
  max-width: 500px;
  width: 100%;
  margin: 0 auto;
  padding: 20px;
  font-family: Arial, sans-serif;
}



h1 {
  text-align: center;
  font-size: 2rem;
  font-weight: 800;
  margin-bottom: 24px;
  background: linear-gradient(to right, #6366f1, #a855f7);
  -webkit-background-clip: text;
  background-clip: text;
  -webkit-text-fill-color: transparent;
}

input[type="checkbox"] {
  width: 18px;
  height: 18px;
  accent-color: rgb(61, 207, 61);
}

.add-box {
  display: flex;
  gap: 10px;
  margin-bottom: 20px;
}

.add-box input {
  flex: 1;
  padding: 12px 16px;
  border: 2px solid #e5e7eb;
  border-radius: 12px;
  font-size: 1rem;
  transition: all 0.3s ease;
  outline: none;
}


.add-box button {
  padding: 12px 24px;
  background-color: #6366f1;
  font-weight: bold;
  color: white;
  border: none;
  border-radius: 12px;
  font-weight: 600;
  cursor: pointer;
  transition: background-color 0.2s ease;
}

.add-box button:hover {
  background-color: #4f46e5;
}


ul {
  list-style: none;
  padding: 0;
}

.todo-item {
  font-size: 1.4em;
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 8px 4px;
  border-bottom: 1px solid #ddd;
}

.todo-item label {
  display: flex;
  align-items: center;
  gap: 8px;
}

.done {
  text-decoration: line-through;
  color: gray;
}

.delete {
  background: none;
  border: none;
  color: red;
  cursor: pointer;
  font-size: 14px;
}

.inputBox {
  position: relative;
  flex: 1;
}

.inputBox input {
  width: 100%;
  padding: 16px;
  font-size: 1rem;
  border-radius: 14px;
  border: 1px solid #e5e7eb;
  background: #f9fafb;
  outline: none;
  transition: 0.3s ease;
}

.inputBox input:focus {
  border-color: #6366f1;
  background: #ffffff;
}

.inputBox span {
  position: absolute;
  left: 16px;
  top: 50%;
  transform: translateY(-50%);
  font-size: 1rem;
  color: #9ca3af;
  pointer-events: none;
  transition: 0.3s ease;
  background: transparent;
  padding: 0 6px;
}

.inputBox input:focus + span,
.inputBox input:not(:placeholder-shown) + span {
  top: -6px;
  font-size: 0.75rem;
  color: #ffffff;
  background: #6366f1;
  border-radius: 999px;
}


/* Responsive */
@media (max-width: 600px) {
  .container {
    margin: 10px;
  }
}
</style>
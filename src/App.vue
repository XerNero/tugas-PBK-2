<script setup>
import { ref, Transition, watch, onMounted } from 'vue'
import gsap from 'gsap'

const newTodo = ref('')
const todos = ref([])

const addTodo = () => {
  if (newTodo.value) {
    const id = Math.random()
    todos.value = [{ text: newTodo.value, id }, ...todos.value]
    newTodo.value = ''
  }
}

const deleteTodo = (id) => {
  todos.value = todos.value.filter((todo) => {
    return todo.id !== id
  })
}

const beforeEnter = (el) => {
  el.style.opacity = 0
  el.style.transform = 'translateY(60px)'
}

const enter = (el, done) => {
  gsap.to(el, 1, {
    y: 0,
    opacity: 1,
    onComplete: done,
  })
}

onMounted(() => {
  const initTodos = localStorage.getItem('todos')
  if (initTodos) {
    todos.value = JSON.parse(initTodos)
  }

  watch(todos, () => {
    localStorage.setItem('todos', JSON.stringify(todos.value))
  })
})
</script>

<template>
  <Transition name="fade" @before-enter="beforeEnter" @enter="enter" appear>
    <div class="container">
      <h1 align="center" class="slide-in">Kegiatan Harian</h1>
      <h3 align="center" class="slide-in1">Naufal Rizqullah</h3>
      <h3 align="center" class="slide-in2">223510805</h3>
      <div align="center" class="todos" style="margin-top: 5%;">
        <input
          type="text"
          placeholder="Tambahkan Kegiatan"
          class="mb-3 md:mb-0 md:mr-3 h-10 md:w-80 w-full rounded-md px-2 text-sm shadow-md transition-shadow duration-200 ease-in focus:shadow-teal-500/50 focus:outline-none"
          v-model="newTodo"
          @keyup.enter="addTodo"
        />
        <Transition name="switch" mode="out-in">
          <div v-if="todos.length">
            <TransitionGroup tag="ul" name="list" appear>
              <li
                v-for="todo in todos"
                :key="todo.id"
                @click="deleteTodo(todo.id)"
                class="mb-3 md:mb-0 md:mr-3 grid h-12 w-80 place-items-center rounded-md bg-white text-center shadow-md transition-shadow duration-300 ease-in hover:cursor-pointer hover:shadow-lg active:shadow-teal-500/50"
              >
                {{ todo.text }}
              </li>
            </TransitionGroup>
          </div>
          <div v-else class="text-center">
            <span class="opacity-50">Tidak Ada Kegiatan</span>
          </div>
        </Transition>
      </div>
    </div>
  </Transition>
</template>

<style scoped>
.container {
  max-width: 700px;
  margin: auto;
  border: 3px solid black;
  border-radius: 10px;
  padding: 20px;
  background-size: cover;
  background-position: center;
  background-color: rgba(255, 255, 255, 10);
  background-image: url('/src/assets/bulan.jpg');
}

.slide-in {
  animation: slideIn 0.5s ease-in-out;
  font-size: 2rem;
  color: #fff;
  font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif; 
}

/* list animation */
.list-enter-from {
  opacity: 0;
  transform: scale(0.6);
}
.list-enter-to {
  opacity: 1;
  transform: scale(1);
}
.list-enter-active {
  transition: all 0.4s ease;
}

.list-leave-from {
  opacity: 1;
  transform: scale(1);
}
.list-leave-to {
  opacity: 0;
  transform: scale(0.6);
}
.list-leave-active {
  transition: all 1s ease;
}

/* switch List */
.switch-enter-from,
.switch-leave-to {
  opacity: 0;
  transform: translateY(20px);
}
.switch-enter-to,
.switch-leave-from {
  opacity: 1;
  transform: translateY(0px);
}
.switch-enter-active,
.switch-leave-active {
  transition: all 0.4s ease;
}

.todos,
.todos input {
  color: black;
}

.todos span {
  color: white;
}
</style>
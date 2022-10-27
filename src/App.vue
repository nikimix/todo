<template>
  <h1>ToDo list</h1>
  <form class="new-todo-item" @submit.prevent="addTodoItem">
    <input class="new-todo-item__input" type="text" v-model="todoTitle" placeholder="Add TO DO">
    <button class="new-todo-item__button" type="submit" :disabled="!isDisableSubmitButton">ADD</button>
  </form>

  <div class="todo-list" v-if="todos.length">
    <h4 class="todo-list__header">Task</h4>
    <h4 class="todo-list__header">Action</h4>
    <TodoItem
        v-for="(item, index) of todos"
        :key="index"
        :title="item.todo"
        :id="index"
        :completed="item.completed"
        @update-todo-item="updateTodoItem"
        @delete-todo-item="deleteTodoItem"
    />
  </div>
</template>

<script setup>
import { computed, ref } from 'vue'
import TodoItem from './components/TodoItem.vue'

const todos = ref([])
const todoTitle = ref('')
const isDisableSubmitButton = computed(() => !!todoTitle.value)

const addTodoItem = async () => {
  const res = await fetch('https://dummyjson.com/todos/add', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({
      todo: todoTitle.value,
      completed: false,
      userId: 1,
    })
  })
  const todo = await res.json()
  todos.value.push(todo)
  todoTitle.value = ''
}

const deleteTodoItem = async (id) => {
  console.log(id)
  const res = await fetch(`https://dummyjson.com/todos/1`, {
    method: 'DELETE',
  })
  const todo = await res.json()
  todos.value = todos.value.filter((item, index) => index !== id)
}

const updateTodoItem = async (id, isDone) => {
  const res = await fetch(`https://dummyjson.com/todos/1`, {
    method: 'PUT',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({
      completed: isDone,
    })
  })
  todos.value[id] = await res.json()
}
</script>

<style scoped>
.new-todo-item {
  display: grid;
  grid-template-columns: repeat(2, auto);
  justify-content: center;
  column-gap: 20px;
}

.new-todo-item__input {
  font-size: 18px;
  border: none;
  border-bottom: 2px solid gray;
  outline: none;
}

.new-todo-item__button:disabled {
  color: gray;
  background-color: lightgray;
  cursor: auto;
}

.todo-list {
  padding: 40px;
  display: grid;
  grid-template-columns: repeat(2, auto);
  align-items: center;
  row-gap: 10px;
  column-gap: 15px;
}

.todo-list__header {
  margin: 0;
}
</style>

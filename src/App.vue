<template>
  <div class="todo-list">
    <h1 class="todo-list__title">ToDo list</h1>
    <div class="new-todo">
      <input
          class="new-todo__input"
          @keydown.enter="addNewTodo"
          type="text"
          v-model="newTodoTitle"
          placeholder="Add TO DO">
      <button
          class="button"
          @click="addNewTodo"
          @keydown.enter="addNewTodo"
          :disabled="!isDisabledSubmitButton">
        ADD
      </button>
    </div>
    <div class="todos"
         v-if="todos.length">
      <p class="todos__title">Task</p>
      <p class="todos__title">Action</p>
      <todo-item
          v-for="item of todos"
          :key="item.id"
          :todoData="item"
          @delete-todo="deleteTodo"
      ></todo-item>
    </div>
    <p v-if="isLoading">Loading...</p>
  </div>
</template>

<script setup>
import { computed, ref } from 'vue';
import TodoItem from './components/TodoItem.vue';

const isLoading = ref(true);
const uniqueId = ref(0);
const newTodoTitle = ref('');
const isDisabledSubmitButton = computed(() => !!newTodoTitle.value);
const todos = ref([]);

const setUniqueId = () => {
  let max = 0;
  todos.value.forEach(({ id }) => {
    if (id > max) {
      max = id;
    }
  });
  uniqueId.value = max;
};

const getAllTodos = async () => {
  const res = await fetch('https://dummyjson.com/todos');
  return await res.json();
};

getAllTodos().then(data => {
  isLoading.value = false;
  todos.value = data.todos;
  setUniqueId();
});

const addNewTodo = () => {
  const id = uniqueId.value += 1;
  const newTodo = {
    todo: newTodoTitle.value,
    id,
    completed: false,
    userId: id,
  };
  todos.value.unshift(newTodo);
  newTodoTitle.value = '';
};

const deleteTodo = (id) => {
  todos.value = todos.value.filter(item => item.id !== id);
};
</script>

<style scoped lang="scss">

.todo-list {
  display: grid;
  justify-items: center;
  align-items: center;
  row-gap: 30px;
  padding: 100px 0;

  &__title {
    margin: 0;
    color: #424cd5;
  }
}

.new-todo {
  display: flex;
  align-items: center;
  width: 100%;
  padding: 0 1rem;
  column-gap: 3rem;

  &__input {
    width: 100%;
    height: min-content;
    padding: .3rem;
    font-size: 1em;
    border: none;
    border-bottom: 1px solid black;
    outline: none;
  }
}

.todos {
  display: grid;
  grid-template-columns: 2fr 1.3fr;
  justify-items: center;
  row-gap: .5rem;

  &__title {
    font: {
      weight: 600;
    }
    color: black;
  }
}
</style>
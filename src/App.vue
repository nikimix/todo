<template>
  <div class="todo-list">
    <h1 class="todo-list__title">ToDo list</h1>
    <div class="todo-list__new-todo new-todo">
      <input
          v-model="newTodoTitle"
          class="new-todo__input"
          type="text"
          placeholder="Add TO DO"
          @keydown.enter="addNewTodo"
      >
      <button
          :disabled="!isDisabledSubmitButton"
          class="new-todo__button button"
          @keydown.enter="addNewTodo"
          @click="addNewTodo"
      >
        ADD
      </button>
    </div>
    <div
        v-if="todos.length"
        class="todo-list__todos todos"
    >
      <div class="todos__row">
        <div>Task</div>
        <div>Action</div>
      </div>
      <todo-item
          v-for="todo of todos"
          :key="todo.id"
          :todo="todo"
          class="todos__row"
          @delete-todo="deleteTodo"
      ></todo-item>
    </div>
    <p v-if="isLoading">Loading...</p>
  </div>
</template>

<script setup>
import {computed, onMounted, ref} from 'vue';
import TodoItem from './components/TodoItem.vue';

const isLoading = ref(false);
const newTodoTitle = ref('');
const isDisabledSubmitButton = computed(() => !!newTodoTitle.value);
const todos = ref([]);

const getUniqueId = () => {
  return todos.value.reduce((acc, {id}) => acc > id ? acc : id, 0) + 1;
};

const getAllTodos = async () => {
  const res = await fetch('https://dummyjson.com/todos');
  return await res.json();
};

onMounted(async () => {
  isLoading.value = true;
  todos.value = (await getAllTodos()).todos;
  isLoading.value = false;
});

const resetNewTodoTitle = () => {
  newTodoTitle.value = '';
};

const addNewTodo = () => {
  const newTodo = {
    todo: newTodoTitle.value,
    id: getUniqueId(),
    completed: false,
    userId: 1,
  };
  todos.value.unshift(newTodo);
  resetNewTodoTitle();
};

const deleteTodo = (id) => {
  const index = todos.value.findIndex((todo) => todo.id === id);
  todos.value.splice(index, 1);
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
  row-gap: .5rem;

  &__row {
    display: grid;
    align-items: center;
    justify-items: start;
    grid-template-columns: 2fr 1.3fr;
    column-gap: .5rem;
    font: {
      weight: 600;
    }
    color: black;
  }
}
</style>
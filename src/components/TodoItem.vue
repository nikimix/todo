<template>
  <input :class="className" ref="input" @focusout="forbiddenEditingTodoItem"
         @keydown.enter="forbiddenEditingTodoItem"
         v-model="title"
         :disabled="!isAllowEditTodoItem"
  />
  <ul class="todo-item__buttons-list">
    <li>
      <button class="todo-item__button todo-item__button--edit" @click="allowEditingTodoItem">EDIT</button>
    </li>
    <li>
      <button class="todo-item__button todo-item__button--done" @click="updateTodoItem(true)" :disabled="props.completed">DONE</button>
    </li>
    <li>
      <button class="todo-item__button todo-item__button--delete" @click="deleteTodoItem">DELETE</button>
    </li>
  </ul>
</template>

<script setup>
import { ref, computed, nextTick } from 'vue'

const emit = defineEmits(['updateTodoItem', 'deleteTodoItem'])
const props = defineProps({
  title: {
    type: String,
    default: '',
  },
  id: {
    type: Number,
    default: 0
  },
  completed: {
    type: Boolean,
    default: false,
  }
})

const title = ref(props.title)
const isAllowEditTodoItem = ref(false)
const input = ref(null)

const className = computed(() => {
  const commonClass = 'todo-item__title'
  const edit = isAllowEditTodoItem.value ? `${commonClass}--edit` : ''
  const done = props.completed ? `${commonClass}--done` : ''
  return `${commonClass} ${edit}${done}`
})

const updateTodoItem = (isDone) => {
  emit('updateTodoItem', props.id, isDone)
}

const deleteTodoItem = () => {
  emit('deleteTodoItem', props.id)
}

const allowEditingTodoItem = () => {
  isAllowEditTodoItem.value = true
  nextTick(() => input.value.focus())
}
const forbiddenEditingTodoItem = () => {
  isAllowEditTodoItem.value = false
  if (props.completed) {
    updateTodoItem(false)
  }
}

</script>


<style scoped>
.todo-item__title {
  display: flex;
  min-width: min-content;
  font-size: 1rem;
  text-align: center;
  justify-items: center;
  background-color: transparent;
  border: none;
  color: black;
  resize: none;
}

.todo-item__title--active {
  border: 1px solid black;
  cursor: pointer;
}

.todo-item__title--done {
  text-decoration: line-through;
}

.todo-item__buttons-list {
  margin: 0;
  padding: 0;
  display: flex;
  column-gap: 15px;
  list-style: none;
}

.todo-item__button {
  min-width: 100px;
}

.todo-item__button--edit {
  border-color: #646cff;
  color: #646cff;
}

.todo-item__button--done {
  border-color: deeppink;
  color: deeppink;
}

.todo-item__button--delete {
  border-color: gray;
  color: gray;
}
</style>
<template>
  <div
      :class="todoState"
      class="todo"
  >
    <div
        @input="updateTodoTitle"
        contenteditable="true"
        class="todo__title"
    >
      {{title}}
    </div>
    <div class="todo__controls todo-controls">
      <button
          @click="toggleStateCompleted"
          class="todo-controls__button todo-controls__button_done button "
      >
        DONE
      </button>
      <button
          @click="deleteTodo"
          class="todo-controls__button todo-controls__button_delete button"
      >
        DELETE
      </button>
    </div>
  </div>
</template>

<script setup>
import { computed, toRefs } from 'vue';

const emit = defineEmits(['deleteTodo']);
const props = defineProps({
  todo: {
    type: Object,
    required: true,
  },
});

const { todo: title, completed, id } = toRefs(props.todo);

const todoState = computed(() => ({
  'done': completed.value,
}));

const toggleStateCompleted = () => {
  completed.value = !completed.value;
};

const deleteTodo = () => {
  emit('deleteTodo', id.value);
};

const updateTodoTitle = (event) => {
  title.value = event.target.textContent;
}

</script>

<style scoped lang="scss">
.todo {
  &__input {
    padding: .3rem;
  }

  &__title {
    max-width: 300px;
    margin: 0;
    font-size: 1em;
    font-weight: 500;
    cursor: pointer;
  }
}

.todo-controls {
  display: flex;
  justify-content: space-between;
  column-gap: .5rem;

  &__button {
    border-color: inherit;

    &_done {
      color: deeppink;
    }
  }
}

.done {
  .todo__title {
    text-decoration: line-through;
  }
}
</style>
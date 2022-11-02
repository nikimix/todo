<template>
  <div
      :class="todoState"
      class="todo"
  >
    <div
        contenteditable="true"
        class="todo__title"
        @input="updateTodoTitle"
    >
      {{title}}
    </div>
    <div class="todo__controls controls">
      <button
          class="controls__button button button_done"
          @click="toggleStateCompleted"
      >
        DONE
      </button>
      <button
          class="controls__button button"
          @click="deleteTodo"
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

.controls {
  display: flex;
  justify-content: space-between;
  column-gap: .5rem;
}

.done {
  .todo__title {
    text-decoration: line-through;
  }
}
</style>
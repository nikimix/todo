<template>
  <div class="todo" :class="todoState">
    <input
        v-show="editableMode"
        @blur="doneEdit"
        @keydown.enter="doneEdit"
        @keydown.esc="doneEdit"
        class="todo__input"
        ref="input"
        v-model="todo"
    />
    <p
        class="todo__title"
        @click="editTodo"
        v-show="!editableMode">
      {{ todo }}
    </p>
    <div class="todo__actions">
      <button
          class="button todo__button_done"
          @click="toggleStateDone">
        DONE
      </button>
      <button
          class="button todo__button_delete"
          @click="deleteTodo">
        DELETE
      </button>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, toRefs, nextTick } from 'vue';

const emit = defineEmits(['deleteTodo']);
const { todoData } = defineProps({
  todoData: {
    type: Object,
    required: true,
  },
});

const { todo, completed, id } = toRefs(todoData);
const editableMode = ref(false);
const input = ref(null);

const todoState = computed(() => ({
  'done': completed.value,
}));

const toggleStateDone = () => {
  completed.value = !completed.value;
};

const deleteTodo = () => {
  emit('deleteTodo', id.value);
};

const editTodo = () => {
  editableMode.value = true;
  nextTick(() => {
    input.value.focus();
  });
};

const doneEdit = () => {
  editableMode.value = false;
};

</script>

<style scoped lang="scss">
.todo {
  display: grid;
  align-items: center;
  grid-template-columns: 2fr 1.3fr;
  column-gap: .5rem;
  grid-column: 1 / 3;
  width: 100%;

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

  &__actions {
    display: flex;
    justify-content: space-between;
    column-gap: .5rem;
  }

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
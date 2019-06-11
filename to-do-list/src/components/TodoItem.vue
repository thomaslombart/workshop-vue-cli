<template>
  <li class="todo-item-container">
    <input type="checkbox" @change="$emit('toggle', todo)" :checked="todo.done">
    <div class="todo-item">
      <span
        v-if="!isEditing"
        @click="isEditing = true"
        class="todo-name"
        :class="{'todo-name--done': todo.done}"
      >{{ todo.name }}</span>
      <input
        v-else
        class="todo-edit"
        type="text"
        v-model="editedTodo"
        @keyup.enter="edit"
        @keyup.esc="isEditing = false"
      >
      <svg
        @click="$emit('delete', index)"
        v-if="!isEditing"
        class="delete-icon"
        role="button"
        xmlns="http://www.w3.org/2000/svg"
        viewBox="0 0 20 20"
      >
        <title>Close</title>
        <path
          d="M14.348 14.849a1.2 1.2 0 0 1-1.697 0L10 11.819l-2.651 3.029a1.2 1.2 0 1 1-1.697-1.697l2.758-3.15-2.759-3.152a1.2 1.2 0 1 1 1.697-1.697L10 8.183l2.651-3.031a1.2 1.2 0 1 1 1.697 1.697l-2.758 3.152 2.758 3.15a1.2 1.2 0 0 1 0 1.698z"
        ></path>
      </svg>
    </div>
  </li>
</template>

<script>
export default {
  props: ["todo", "index"],
  data() {
    return {
      isEditing: false,
      editedTodo: this.todo.name
    };
  },
  methods: {
    edit() {
      this.$emit("edit", this.editedTodo, this.index);
      this.isEditing = false;
    }
  }
};
</script>

<style scoped>
.todo-item-container {
  margin-left: 0;
  display: flex;
  align-items: center;
  margin-top: 0.5rem;
  padding: 1rem;
  background-color: #f7fafc;
}

.todo-item {
  display: flex;
  align-items: center;
  justify-content: space-between;
  width: 100%;
}

.todo-name {
  margin-left: 1rem;
  cursor: pointer;
}

.todo-name--done {
  text-decoration: line-through;
  color: #4a5568;
}

.todo-edit {
  font-size: 1rem;
  margin-left: 0.75rem;
  margin-right: 2rem;
  outline: none;
  padding: 0.5rem;
  border: 2px solid #e2e8f0;
  border-radius: 0.25rem;
  width: 100%;
}

.delete-icon {
  cursor: pointer;
  fill: #c53030;
  height: 20px;
  width: 20px;
}
</style>

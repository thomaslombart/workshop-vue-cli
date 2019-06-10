<template>
  <li class="flex p-4 items-center mt-2 bg-gray-100">
    <input type="checkbox" class="-mt-1" @change="$emit('toggle', todo)" :checked="todo.done">
    <div class="flex items-center justify-between w-full">
      <span
        v-if="!isEditing"
        @click="isEditing = true"
        class="ml-4 cursor-pointer"
        :class="{'line-through text-gray-700': todo.done}"
      >{{ todo.name }}</span>
      <input
        v-else
        class="ml-4 mr-2 outline-none p-2 border-gray-300 rounded border-2 w-full"
        type="text"
        v-model="editedTodo"
        @keyup.enter="edit"
        @keyup.esc="isEditing = false"
      >
      <svg
        @click="$emit('delete', index)"
        v-if="!isEditing"
        class="fill-current h-5 w-5 text-red-700"
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

<style>
</style>

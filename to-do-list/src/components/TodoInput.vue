<template>
  <input
    v-model="nextTodo"
    class="mt-4 mb-4 p-3 bg-gray-100 outline-none w-full rounded leading-tight"
    @keyup.enter="emitAddTodo"
    placeholder="Type your task here"
  >
</template>

<script>
export default {
  data() {
    return {
      nextTodo: ""
    };
  },
  methods: {
    emitAddTodo() {
      if (this.nextTodo) {
        this.$emit("addTodo", this.nextTodo);
        this.nextTodo = "";
      }
    }
  }
};
</script>

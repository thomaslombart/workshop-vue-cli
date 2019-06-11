<template>
  <input
    v-model="nextTodo"
    class="input"
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

<style scoped>
.input {
  margin-top: 2rem 0;
  padding: 1rem;
  background-color: #f7fafc;
  outline: none;
  box-sizing: border-box;
  font-size: 1rem;
  width: 100%;
  border: none;
  border-radius: 0.25rem;
  line-height: 1.25;
}
</style>

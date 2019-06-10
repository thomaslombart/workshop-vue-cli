<template>
  <div id="app" class="container mx-auto p-8 md:w-1/2">
    <h2 class="text-3xl text-gray-900 uppercase tracking-wide">To-Do App</h2>
    <todo-input @addTodo="addTodo"></todo-input>
    <ul class="list-reset" v-if="filteredTodos.length">
      <todo-item
        v-for="(todo, i) in filteredTodos"
        :key="`${todo.name}-${i}`"
        :todo="todo"
        :index="i"
        @toggle="toggle"
        @delete="deleteItem"
        @edit="editItem"
      ></todo-item>
    </ul>
    <p
      v-else
      class="bg-gray-100 p-2 w-full text-center text-xl border border-gray-300 font-bold text-gray-700"
    >No to-dos!</p>
    <todo-filters @changeCurrentFilter="changeCurrentFilter" :currentFilter="currentFilter"></todo-filters>
  </div>
</template>

<script>
import TodoFilters from "./components/TodoFilters";
import TodoInput from "./components/TodoInput";
import TodoItem from "./components/TodoItem";

export default {
  name: "app",
  components: {
    TodoFilters,
    TodoInput,
    TodoItem
  },
  data() {
    return {
      todos: [],
      currentFilter: "all"
    };
  },
  mounted() {
    const localTodos = localStorage.getItem("vue-todos");
    if (localTodos) {
      this.todos = JSON.parse(localTodos);
    }
  },
  watch: {
    todos: {
      handler() {
        localStorage.setItem("vue-todos", JSON.stringify(this.todos));
      },
      deep: true
    }
  },
  computed: {
    filteredTodos() {
      switch (this.currentFilter) {
        case "all":
          return this.todos;
        case "active":
          return this.todos.filter(todo => !todo.done);
        case "done":
          return this.todos.filter(todo => todo.done);
        default:
          return this.todos;
      }
    }
  },
  methods: {
    addTodo(nextTodo) {
      this.todos = [{ name: nextTodo, done: false }, ...this.todos];
    },
    toggle(todo) {
      todo.done = !todo.done;
    },
    deleteItem(index) {
      this.todos.splice(index, 1);
    },
    editItem(name, index) {
      this.todos[index].name = name;
    },
    changeCurrentFilter(filter) {
      this.currentFilter = filter;
    }
  }
};
</script>

<style>
#app {
  font-family: Roboto, Helvetica, Arial, sans-serif;
}
</style>

<template>
  <div id="app">
    <h2 class="title">To-Do App</h2>
    <todo-input @addTodo="addTodo"></todo-input>
    <ul class="list" v-if="filteredTodos.length">
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
    <p v-else class="no-results">No to-dos!</p>
    <todo-filters
      class="filters"
      @changeCurrentFilter="changeCurrentFilter"
      :currentFilter="currentFilter"
    ></todo-filters>
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

<style scoped>
#app {
  margin: 0 auto;
  padding: 2rem;
}

.title {
  font-size: 2rem;
  color: #1a202c;
  text-transform: uppercase;
  letter-spacing: 0.025em;
}

.list {
  list-style: none;
  padding: 0;
}

.no-results {
  background-color: #f7fafc;
  padding: 0.5rem;
  width: 100%;
  text-align: center;
  font-size: 1.4rem;
  font-weight: bold;
  color: #4a5568;
  border: 1px solid #e2e8f0;
}

.filters {
  margin-top: 1rem;
}

@media screen and (min-width: 768px) {
  #app {
    width: 50%;
  }
}
</style>

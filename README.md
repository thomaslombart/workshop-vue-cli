# Vue basics: part 2

**Important**: this workshop assumes you know the very basics of Vue (reactivity, directives like `v-if`, `v-for`, components and computed properties). If you don't please check the first [workshop](https://github.com/thomlom/workshop-vue-basics).
 
## Explaining a To-Do list

In the last workshop, your task was to build a to-do app that would allow you to add tasks, mark them as completed, edit and delete them. You could also filter them depending on their status and provide some design. Let's see how one can make such an app.

```html
<html>
  <head>
    <meta charset="UTF-8" />
    <title>To-Do App</title>
    <link
      href="https://cdn.jsdelivr.net/npm/tailwindcss/dist/tailwind.min.css"
      rel="stylesheet"
    />
  </head>

  <body>
    <div id="app" class="container mx-auto p-8 md:w-1/2">
      <h2 class="text-3xl text-grey-darkest uppercase tracking-wide">
        To-Do App
      </h2>
      <input
        v-model="nextTodo"
        class="mt-4 mb-4 p-3 bg-grey-lighter outline-none w-full rounded leading-tight"
        @keyup.enter="addTodo"
        placeholder="Type your task here"
      />
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
      <p v-else class="text-center m-8 text-2xl font-bold text-grey-darker">
        No to-dos!
      </p>
      <ul class="list-reset flex mt-3">
        <filter-todo
          v-for="filter in filters"
          @click.native="currentFilter = filter"
          :active="currentFilter === filter"
        >
          {{ filter }}
        </filter-todo>
      </ul>
    </div>
    <script src="https://cdn.jsdelivr.net/npm/vue"></script>
    <script src="main.js"></script>
  </body>
</html>
```

And for the script part:

```js
Vue.component("todo-item", {
  template: `
  	<li class="flex p-4 items-center mt-2 bg-grey-lightest">
      <input type="checkbox" class="-mt-1" @change="$emit('toggle', todo)" :checked="todo.done"/>
      <div class="flex items-center justify-between w-full" >
        <span 
          v-if="!isEditing" 
          @click="isEditing = true" 
          class="ml-4 cursor-pointer" 
          :class="{'line-through text-grey-dark': todo.done}"
        >
        {{ todo.name }}
        </span>
        <input
          v-else
          class="ml-4 mr-2 outline-none p-2 border-grey-light rounded border-2 w-full" 
          type="text" 
          v-model="editedTodo" 
          @keyup.enter="edit"
          @keyup.esc="isEditing = false"
        />
        <svg 
          @click="$emit('delete', index)" 
          class="fill-current h-5 w-5 text-red" 
          role="button" 
          xmlns="http://www.w3.org/2000/svg" 
          viewBox="0 0 20 20"
        >
          <title>Close</title>
          <path d="M14.348 14.849a1.2 1.2 0 0 1-1.697 0L10 11.819l-2.651 3.029a1.2 1.2 0 1 1-1.697-1.697l2.758-3.15-2.759-3.152a1.2 1.2 0 1 1 1.697-1.697L10 8.183l2.651-3.031a1.2 1.2 0 1 1 1.697 1.697l-2.758 3.152 2.758 3.15a1.2 1.2 0 0 1 0 1.698z"/>
        </svg>
      </div>
    </li>
  `,
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
});

Vue.component("filter-todo", {
  template: `
    <li 
      class="rounded-full uppercase px-2 py-1 text-xs font-bold mr-2 cursor-pointer" 
      :class="active ? 'bg-blue-dark text-white' : 'bg-grey-lighter text-grey-darker'"
    >
      <slot></slot>
    </li>
  `,
  props: ["active"]
});

new Vue({
  el: "#app",
  data: {
    todos: [],
    nextTodo: "",
    filters: ["all", "active", "done"],
    currentFilter: "all"
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
    addTodo() {
      if (this.nextTodo) {
        this.todos = [{ name: this.nextTodo, done: false }, ...this.todos];
        this.nextTodo = "";
      }
    },
    toggle(todo) {
      todo.done = !todo.done;
    },
    deleteItem(index) {
      this.todos.splice(index, 1);
    },
    editItem(name, index) {
      this.todos[index].name = name;
    }
  }
});
```

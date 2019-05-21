# Vue basics: part 2

**Important**: this workshop assumes you know the very basics of Vue (reactivity, directives like `v-if`, `v-for`, components and computed properties). If you don't please check the first [workshop](https://github.com/thomlom/workshop-vue-basics).
 
## Explaining a To-Do list

In the last workshop, your task was to build a to-do app that would allow you to add tasks, mark them as completed, edit and delete them. You could also filter them depending on their status and provide some design. Here is the full code:

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
        <li
          v-for="filter in filters"
          @click="currentFilter = filter"
          class="rounded-full uppercase px-2 py-1 text-xs font-bold mr-2 cursor-pointer"
          :class="currentFilter === filter ? 'bg-blue-dark text-white' : 'bg-grey-lighter text-grey-darker'"
        >
          {{ filter }}
        </li>
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

new Vue({
  el: "#app",
  data: {
    todos: [],
    nextTodo: "",
    filters: ["all", "active", "done"],
    currentFilter: "all"
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

Here are the different steps to build this app:

1. Provide the basic HTML template and render the Vue app inside a root element
2. Decide the model of what's a todo (a `name` and whether it's `done` or not). Provide an array of todos to your `data`.
3. Render an initial list of todos in the template (don't forget to use `key`!)
4. Add an input and bind it to a data property (`nextTodo`) using `v-model`.
5. Create an add button or use a `keyup` event to add the todo in the list of todos. 
6. Add the `todo` in the list of todos (in my case, I've added the todo in the first place, so that the newly todo shows up first)
7. Add a checkbox to mark the todo as completed or not.
8. Move the checkbox and the name of a todo inside a new component: `todo-item`. Add the `v-for` directive on the component. Pass the `index` and the `todo` to this component as a prop.
9. Add an event listener to the checkbox and mark it as checked based on the value of `todo.done`. If the checkbox is toggled, send a custom event named `toggle`.
10. Capture the `toggle` event in the main component and toggle the todo.
11. Add an icon to the todo to delete it. 
12. Add a `click` event on this icon to emit a `delete` event to the parent component.
13. Capture the `delete` event on the parent component and delete the todo.
14. Add an edit mode to the todo by rendering an input if you click on the todo's name.
15. Send the edited todo to the parent using a custom event named `edit` to edit the todo. You can send this event on clicking on a button. In my case, I've chosen to send this event when the user taps `Enter`.
16. Create an array of three filters in the `data` object: `["all", "active", "done"]`
17. Render the filters in the main component using `v-for`.
18. Add a `currentFilter` property to the data and set the initial value to `all`.
19. Add a `click` event to the filter item that will set the `currentFilter` to the filter that's being clicked.
20. Add a computed property called `filteredTodos` to the main instance and filter the correct todos depending on the value of `currentFilter`.
21. In the `v-for` statement in `todo-item`, replace the `todos` list by the `filteredTodos` computed property.
22. Differentiate the filter by styling the active one.
23. To save the todos in the local storage, add a **deep** watcher on the `todos` array. This watcher will be triggered on every change and will set the todos in the local storage.
24. When the main component is mounted fetch the todos in the local storage and set them in the data.

Some takeaways from this code:

- The design have been done with [Tailwind CSS](https://tailwindcss.com/). It's a utility-first CSS framework so there are a loooot of classes. It may makes the template code bloated but it's very handy when you're used to.
- It's no big deal if you have trouble understanding the full code. The goal here is to globally understand how Vue works and how we can apply what we've learnt on a real app.

## Vue CLI

In this part, we will just look at a very basic project created with Vue CLI. It's an awesome tool because it helps us setting our Vue project that provides support for a lot of popular JS tools such as Webpack, Babel, etc.

As a prerequisite, we will need [Node.js](https://nodejs.org/en/) to be installed on our machines. Optionally You can also install [Yarn](https://yarnpkg.com/).

**Note** : if you're a macOS user, you can install these tools faster using [Homebrew](https://brew.sh/), just run `brew install node` and `brew install yarn`.

Open a terminal and run `yarn global add @vue/cli` to install Vue CLI globally. Then, run `vue --version` and make sure you have the version 3.

Final steps :

1. Run `vue create movies`
2. Select `Manually select features`
3. Select `Babel, Linter/Formatter and Unit Testing`
4. Select `ESLint + Standard config`
5. Select `Lint on save`
6. Select `Jest` as unit testing solution
7. Select `In dedicated config files` for Babel, PostCSS, etc.
8. Answer yes or no, depends on if you like this preset!
9. Select `Use Yarn` as package manager
10. You should see a progress bar that is gradually filling...
11. You see `🎉 Successfully created project movies`
12. `cd` into your project : `cd movies`
13. Run `yarn serve` 
14. Open your browser and visit `http://localhost:8080/`

Congratulations! You have built your first Vue app.

Open your project in your favorite code editor (I recommend [VS Code](https://code.visualstudio.com/)).

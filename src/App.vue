<script>
export default {
  name: 'App',
  data: () => ({
    todos: [],
    newTodo: '',
    editedTodo: null,
    visibility: 'all'
  }),

  computed: {
    activeTasks(){
      return this.todos.filter(todo => !todo.completed)
    },
    filteredTodos(){
      if(this.visibility === 'all'){
        return this.todos
      }else if(this.visibility === 'active'){
        return this.activeTasks
      }else{
        return this.todos.filter(todo => todo.completed)
      }
    },
    remaining(){
      return this.activeTasks.length
    },
    allDone: {
      get(){
        return this.remaining === 0
      },
      set(value){
        this.todos.forEach(todo => {
          todo.completed = value
        })
      }
    }
  },

  // methods that implement data logic.
  // note there's no DOM manipulation here at all.
  methods: {
    addTodo(){
      const value = this.newTodo && this.newTodo.trim()
      const todoItem = {
        id: this.todos.length + 1,
        title: value,
        completed: false
      }

      if(!value){
        return
      }
      this.todos.push(todoItem)
      this.newTodo = ''
    },

    cancelEdit(todo){
      this.editedTodo = null
      todo.title = this.beforeEditCache
    },

    doneEdit(todo){
      if(!this.editedTodo){
        return
      }
      this.editedTodo = null
      todo.title = todo.title.trim()
      if(!todo.title){
        this.removeTodo(todo)
      }
    },

    editTodo(todo){
      this.beforeEditCache = todo.title
      this.editedTodo = todo
    },

    pluralize(word, count){
      return word + (count === 1 ? '' : 's')
    },

    removeCompleted(){
      this.todos = this.todos.filter(item => {
        return !item.completed
      })
    },

    removeTodo(todo){
      const index = this.todos.indexOf(todo)
      this.todos.splice(index, 1)
    },

    updateTodo(todo){
      this.todos.find(item => item === todo).completed = !todo.completed
    }
  }
}
</script>

<template>
  <section class="todoapp">
    <header class="header">
      <h1>todos</h1>
      <input
          class="new-todo"
          autofocus
          autocomplete="off"
          placeholder="What needs to be done?"
          v-model="newTodo"
          @keyup.enter="addTodo"
      />
    </header>
    <section class="main" v-show="todos.length">
      <input
          id="toggle-all"
          class="toggle-all"
          type="checkbox"
          v-model="allDone"
      />
      <label for="toggle-all">Mark all as complete</label>
      <ul class="todo-list">
        <li
            class="todo"
            v-for="todo in filteredTodos"
            :key="todo.id"
            :class="{ completed: todo.completed, editing: todo == editedTodo }"
        >
          <div class="view">
            <input
                class="toggle"
                type="checkbox"
                @click="updateTodo(todo)"
                :checked="todo.completed"
            />
            <label @dblclick="editTodo(todo)">{{ todo.title }}</label>
            <button class="destroy" @click="removeTodo(todo)"></button>
          </div>
          <input
              class="edit"
              type="text"
              v-model="todo.title"
              @blur="doneEdit(todo)"
              @keyup.enter="doneEdit(todo)"
              @keyup.esc="cancelEdit(todo)"
          />
        </li>
      </ul>
    </section>
    <footer class="footer" v-show="todos.length">
      <span class="todo-count">
        <strong v-text="remaining"></strong>
        {{ pluralize('item', remaining) }} left
      </span>
      <ul class="filters">
        <li>
          <button
              @click="visibility = 'all'"
              :class="{ selected: visibility == 'all' }"
              class="btn"
          >
            All
          </button>
        </li>
        <li>
          <button
              @click="visibility = 'active'"
              :class="{ selected: visibility == 'active' }"
              class="btn"
          >
            Active
          </button>
        </li>
        <li>
          <button
              @click="visibility = 'completed'"
              :class="{ selected: visibility == 'completed' }"
              class="btn"
          >
            Completed
          </button>
        </li>
      </ul>
      <button
          class="clear-completed"
          @click="removeCompleted"
          v-show="todos.length > remaining"
      >
        Clear completed
      </button>
    </footer>
  </section>
  <footer class="info">
    <p>Double-click to edit a todo</p>
    <p>
      A Vue 3 iteration on
      <a href="https://todomvc.com/examples/vue/">TodoMVC - Vue</a>
    </p>
  </footer>
</template>

<style>
@import "./styles/todomvc-base.css";
@import "./styles/todomvc-index.css";

#app {
  font-family:Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing:antialiased;
  -moz-osx-font-smoothing:grayscale;
  text-align:center;
  color:#2c3e50;
  margin-top:60px;
}

.btn {
  padding:0 10px;
}
</style>

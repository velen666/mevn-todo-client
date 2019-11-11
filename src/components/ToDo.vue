<template lang="html">
  <div id='wrapper'>
    <h1>Todo List</h1>
    <div v-for='todo in todos' :key='todo._id' class="todo">
      <div class="todo-item">
        <div class="todo-item__title">{{todo.title}}</div>
        <button @click='deleteTodo(todo._id)'>Delete</button>
      </div>
    </div>
    <form v-on:submit='addTodo($event)' class='add-todo'>
      <input type='text' placeholder='Enter Todo' v-model='newTodo'/>
      <button type='submit'>Add +</button>
    </form>
  </div>
</template>

<script>
import ToDoAPI from '@/services/ToDoAPI.js'
export default {
  data () {
    return {
      newTodo: '',
      todos: []
    }
  },
  mounted () {
    this.loadTodos()
    if (localStorage.getItem('todos')) this.todos = JSON.parse(localStorage.getItem('todos'))
  },
  watch: {
    todos: {
      handler() {
        localStorage.setItem('todos', JSON.stringify(this.todos))
      },
      deep: true,
    },
  },
  methods: {
    async addTodo (evt) {
      evt.preventDefault()
      const response = await ToDoAPI.addTodo(this.newTodo)
      this.todos.push(response.data)
      this.newTodo = ''
    },
    deleteTodo (todoID) {
      ToDoAPI.deleteTodo(todoID)
      this.todos = this.todos.filter(function (obj) {
        return obj._id !== todoID
      })
    },
    async loadTodos () {
      const response = await ToDoAPI.getToDos()
      this.todos = response.data
    }
  }
}
</script>

<style lang="scss">
  #wrapper {
    width: 50%;
    margin: 0 auto;
    text-align: center;
  }
  @media all and (max-width: 768px) {
    #wrapper {
      width: calc(100% - 40px);
      margin: 0 20px;
    }
  }

  .todo-item {
    width: 100%;
    display: flex;
    justify-content: space-between;
    margin-bottom: 15px;
    clear: both;
  }
  .add-todo {
    clear: both;
    margin-top: 10px;
    float: left;
  }
  input[type='text'] {
    margin: 0 10px 0 0px;
    padding: 10px;
    border: none;
    outline: none;
    float: left;
    background-color: #00a8ff;
    color: white;
    border-bottom: 1px solid white;
    font-size: 100%
  }
  ::placeholder {
    color: white;
  }
  button {
    margin: 0 10px 0 0px;
    padding: 10px;
    border: none;
    outline: none;
    float: left;
    background-color: #00a8ff;
    color: white;
    border-bottom: 1px solid white;
    font-size: 100%;
    cursor: pointer;
  }
</style>

<template>
  <div class="w-full bg-orange-50">
    <Header title="Todos Tracker" />
    <AddTodo @add-todo="addTodo"/>
    <div v-if="todos.length" class="mt-8">
      <Todos :todos="todos" @delete-todo="deleteTodo" @toggle-complete="toggleComplete"/>
    </div>
    <div v-else>
      <p>Loading todos...</p> 
    </div>
  </div>
</template>

<script>
import Header from "./components/Header.vue";
import AddTodo from "./components/AddTodo.vue";
import Todos from "./components/Todos.vue";
import axios from 'axios';
export default {
  name: "App",
  components: {
    Header,
    Todos,
    AddTodo,
  },
  data() {
    return {
      todos: [],
    };
  },
  methods: {
    async fetchTodos() {
      const response = await axios.get('http://localhost:3000/api/v1/todos')
      return response.data
    },
    async addTodo(todo) {
      const response = await axios.post('http://localhost:3000/api/v1/todos', {todo})
        this.todos.unshift(response.data)
        // this.todos = [...this.todos, todo]
  
    },
    async fetchTodo(id) {
      const res = await axios.get(`http://localhost:3000/api/v1/todos/${id}`)
      return res.data
    },
    async deleteTodo(id) {
      const response = await axios.delete(`http://localhost:3000/api/v1/todos/${id}`)
      if(response.status === 200) {
        this.todos = this.todos.filter((todo) => todo.id !== id)
      }
      this.todos = this.todos.filter((todo) => todo.id !== id)
    },
    async toggleComplete(id) {
      const todoToToggle = await this.fetchTodo(id);
      console.log(todoToToggle)
      const updated = {...todoToToggle, completed: !todoToToggle.completed}
      console.log(updated)
      axios.put(`http://localhost:3000/api/v1/todos/${id}`,
      {
        updated
      },
      {
        headers: {
        'Content-Type': 'application/x-www-form-urlencoded; charset=UTF-8'
        }
      })
      .then(res => res.data)
      .catch(e => console.error(e));

      this.todos = this.todos.map((todo) => 
      todo.id === id ? { ...todo, completed: !todo.completed} : todo
    )
    }
  },
  async created() {
    this.todos = await this.fetchTodos()
  }
};
</script>

<style lang="scss">
* {
  padding: 0;
  margin: 0;
  box-sizing: border-box;
}
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
</style>

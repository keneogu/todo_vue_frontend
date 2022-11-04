<template>
  <div class="w-full bg-orange-50">
    <Header title="Todos Tracker" />
    <AddTodo @add-todo="addTodo"/>
    <div v-if="showModal">
    <Modal theme="sale">
      <p>{{sampleTodo}}</p>
      <br/>
      <button @click="toggleModal" class="bg-white text-orange-800 px-4 py-2 rounded">Close</button>
    </Modal>
  </div>
    <div v-if="todos.length" class="mt-8">
      <button @click="()=>{toggleModal(); clickSample();}" class="bg-orange-800 text-white px-4 py-2 rounded-xl shadow hover:text-orange-800 hover:bg-slate-50">what should i do today</button>
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
import Modal from "./components/Modal.vue"

export default {
  name: "App",
  components: {
    Header,
    Todos,
    AddTodo,
    Modal
  },
  data() {
    return {
      showModal: false,
      todos: [],
      sampleTodo: ""
    };
  },
  methods: {
    toggleModal() {
      this.showModal = !this.showModal
    },
    async fetchTodos() {
      const response = await axios.get('http://localhost:3000/api/v1/todos')
      return response.data
    },
    async addTodo(todo) {
      const response = await axios.post('http://localhost:3000/api/v1/todos', {todo})
        this.todos.unshift(response.data)
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
      {todo: {...updated}})
      .then(res => res.data)
      .catch(e => console.error(e));

      this.todos = this.todos.map((todo) => 
      todo.id === id ? { ...todo, completed: !todo.completed} : todo
    )
    },
    clickSample() {
      this.sampleTodo = this.todos[Math.floor(Math.random()*this.todos.length)].title
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

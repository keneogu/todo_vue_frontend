<template>
  <div class="w-full bg-orange-50">
    <div v-if="loggedin">
      <Header title="Todos Tracker" @logout="handleLogout"/>
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
    <div v-if="!loggedin" class="bg-orange-50">
      <div id="loginform" class="h-full mt-8">
				<form v-on:submit.prevent="handleLogin">
				<fieldset><legend>
					Login
				</legend>
				<input type="text" placeholder="username" v-model="loginUN">
				<input type="password" placeholder="password" v-model="loginPW">
				<input type="submit" value="Login In">
				</fieldset>
				</form>
				<form v-on:submit.prevent="handleSignup">
					<fieldset><legend>
						Sign Up
					</legend>
					<input type="text" placeholder="username" v-model="createUN">
					<input type="password" placeholder="password" v-model="createPW">
					<input type="submit" value="create user">
					</fieldset>
					</form>
			</div>
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
      sampleTodo: "",
      loggedin: false,
      JWT: "",
      createUN: "",
      createPW: "",
      loginUN: "",
      loginPW: "",
      devURL: "http://localhost:3000",
      prodURL: null,
      user: null,
		  token: null,
    };
  },
  methods: {
    toggleModal() {
      this.showModal = !this.showModal
    },
    fetchTodos() {
      axios.get('http://localhost:3000/api/v1/todos',  { headers: { Authorization: `bearer ${this.token}` }})
      .then(response => {
        this.todos = response.data
        })
      .catch(err => console.log(err))
    },
    async addTodo(todo) {
      const response = await axios.post('http://localhost:3000/api/v1/todos', {todo}, { headers: { Authorization: `bearer ${this.token}` }})
        this.todos.unshift(response.data)
    },
    async fetchTodo(id) {
      const res = await axios.get(`http://localhost:3000/api/v1/todos/${id}`, { headers: { Authorization: `bearer ${this.token}` }})
      return res.data
    },
    async deleteTodo(id) {
      const response = await axios.delete(`http://localhost:3000/api/v1/todos/${id}`, { headers: { Authorization: `bearer ${this.token}` }})
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
      {todo: {...updated}}, { headers: { Authorization: `bearer ${this.token}` }})
      .then(res => res.data)
      .catch(e => console.error(e));

      this.todos = this.todos.map((todo) => 
      todo.id === id ? { ...todo, completed: !todo.completed} : todo
    )
    },
    clickSample() {
      this.sampleTodo = this.todos[Math.floor(Math.random()*this.todos.length)].title
    },
    handleLogin: function(e) {
			e.preventDefault()
			const URL = this.prodURL ? this.prodURL : this.devURL
			const user = {username: this.loginUN, password: this.loginPW}
			fetch(`${URL}/login`, {
				method: "post",
				headers: {
					"Content-Type": "application/json"
				},
				body: JSON.stringify(user)
			})
			.then(response => response.json())
			.then(data => {
				if(data.error) {
					alert("error logging in")
				}else {
					this.user = data.user
          this.token = data.token
					this.loggedin = true
					this.fetchTodos()
					this.loginUN = ""
					this.loginPW = ""
					window.sessionStorage.setItem('login', JSON.stringify(data))
				}
			})
    },
    handleLogout: function() {
			this.loggedin = false
			this.user = null
			this.token = null
			window.sessionStorage.removeItem('login')
		},
    handleSignup: function() {
			const URL = this.prodURL ? this.prodURL : this.devURL
			const user = {username: this.createUN, password: this.createPW}
			fetch(`${URL}/users`, {
				method: "post",
				headers: {
					"Content-Type": "application/json"
				},
				body: JSON.stringify(user),
			})
			.then(response => response.json())
			.then(data => {
				if(data.error) {
					alert('Sign up unsuccessful')
				} else {
					this.user = data.user
          this.token = data.token
					this.loggedin = true
					this.fetchTodos()
					alert ('sign up successful')
				}
			})
		},
  },
  async created() {
    const getLogin = JSON.parse(window.sessionStorage.getItem('login'))
		if(getLogin) {
			this.user = getLogin.user
			this.token = getLogin.token
			this.loggedin = true
			this.fetchTodos()
		}
    // this.todos = await this.fetchTodos()
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

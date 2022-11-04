<template>
<div class="flex justify-center items-center flex-col">
	<div :key="todo.id" v-for="todo in todos" class="mt-4 w-full md:w-1/2 flex-col">
		<Todo :todo="todo" @toggle-complete="$emit('toggle-complete', todo.id)" @delete-todo="$emit('delete-todo', todo.id)"/>
	</div>
</div>
</template>

<script>

import Todo from './Todo.vue'

export default {
	name: 'Tasks',
	props: {
		todos: Array,
	},
	components: {
		Todo,
	},
	emits: ['delete-todo', 'toggle-complete']
}
</script>

<style>

</style>
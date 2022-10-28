<template>
	<div class="flex justify-between bg-slate-50 p-4 m-4 shadow-lg rounded" @dblclick="$emit('toggle-complete', todo.id)">
		<h3>{{todo.title}}</h3>
		<p>{{todo.completed}}</p>
		<div>
			<i :class="[todo.completed ? 'completed' : '','fas fa-check-circle']"></i>
		<i @click="$emit('delete-todo', todo.id)" class="fa fa-trash cursor-pointer hover:text-orange-800 pl-8" aria-hidden="true"></i>
		</div>
	</div>
</template>

<script>
export default {
	name: 'Todo',
	props: {
		todo: Object,
	}
}
</script>

<style>
.completed {
	color: green
}
</style>

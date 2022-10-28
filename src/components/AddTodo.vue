<template>
  <form @submit.prevent="onSubmit()" class="m-10">
		<div class="flex justify-center items-center mb-12">
    <div class="mr-2">
      <input type="text" v-model="title" name="title" placeholder="add todo" class="p-2 w-72 rounded-2xl shadow-2xl" />
    </div>
    <input type="submit" value="Submit" class="bg-orange-800 p-2 text-white rounded-xl w-32 cursor-pointer shadow-2xl"/>
		</div>
  </form>
</template>

<script>
export default {
 name: 'AddTodo',
 data() {
	 return {
		 title: '',
		 completed: false,
	 }
 },
 methods: {
	 onSubmit() {
		 	if(!this.title) {
				alert('please add a todo')
				return
		 	}
		 	const newTodo = {
				title: this.title,
				completed: this.completed
		 	}
			this.$emit('add-todo', newTodo)
			this.title = ''
			this.completed = false
  	},
	}
}
</script>

<style>

</style>

<template>
  <header class="w-full bg-orange-800 hover:bg-orange-600 h-10 flex justify-between items-center">
    <h1 class="text-white text-4xl md:text-xl font-bold decoration-dotted underline p-2">{{ title }}</h1>
    <button @click="$emit('logout')" class="text-white">logout</button>
  </header>
</template>

<script>
export default {
  name: 'Header',
  props: {
    title: String,
  }
};
</script>

<style></style>

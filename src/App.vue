<script setup>
import { RouterLink, RouterView } from 'vue-router'
import HelloWorld from './components/HelloWorld.vue'
import AutocompleteComponent from './components/Autocomplete.vue'
</script>

<template>
  <AutocompleteComponent />
</template>

<style scoped>
* {
  padding: 0;
  margin: 0;
  font-family: Arial, Helvetica, sans-serif;
}
</style>

<template>
  <div id="app">
    <playground></playground>
  </div>
</template>

<script>
import Playground from './components/Playground'

export default {
  name: 'App',
  components: {
    Playground
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
  margin-top: 60px;
}
</style>

<template>
  <div class="container-fluid" id="app">
    <Juego />
  </div>
</template>

<script>
import Juego from './components/Juego'

export default {
  name: 'App',
  components: {
    Juego
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  padding: 0;
}
</style>


<template>
  <div class="container-fluid" id="app">
    <Juego />
  </div>
</template>

<script>
import Juego from './components/Juego';

export default {
  name: 'App',
  components: {
    Juego
  }
}
</script>

<style>
#app {

  font-family: -apple-system,BlinkMacSystemFont,Segoe UI,Roboto,Helvetica Neue,Arial,Noto Sans,sans-serif,Apple Color Emoji,Segoe UI Emoji,Segoe UI Symbol,Noto Color Emoji;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  padding: 0;
  margin-left: 0;
  margin-right: 0;
}

.container-fluid {
  padding: 0;
  margin-left: 0;
  margin-right: 0;
}

.col{
  padding: 0;
  margin-left: 0;
  margin-right: 0;
}

.row{
  padding: 0;
  margin-left: 0;
  margin-right: 0;
}
</style>


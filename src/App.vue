<template>
  <div id="app">
    <div class="container">
      <Puzzle />
    </div>
  </div>
</template>

<script>
import Puzzle from "./components/Puzzle.vue";

export default {
  name: "App",
  components: {
    Puzzle
  }
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: white;
  margin-top: 1em;
}
body {
  background: #42b983;
}
</style>

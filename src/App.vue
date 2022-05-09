<template style="backgroung-color: black">
  <div id="app">
    <MovieSearch></MovieSearch>
  </div>
</template>

<script>
import MovieSearch from "./components/MovieSearch.vue";

export default {
  name: "App",
  components: {
    MovieSearch,
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}
</style>

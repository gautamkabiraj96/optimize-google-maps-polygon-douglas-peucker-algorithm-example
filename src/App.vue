<template>
  <div id="app">
    <PolygonComponent />
  </div>
</template>

<script>
import PolygonComponent from "./components/Polygon";

export default {
  name: "App",
  components: {
    PolygonComponent,
  },
};
</script>

<style>
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}
</style>

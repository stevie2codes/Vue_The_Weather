<template>
  <div id="app">
    <Dashboard />
  </div>
</template>

<script>
import Dashboard from "./components/Dashboard";
export default {
  name: "App",

  components: {
    Dashboard
  }
};
</script>

<style>
#app {
  font-family: "Questrial", sans-serif;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>

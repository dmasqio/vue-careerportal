<template>
  <HelloWorld msg="Hello Vue 3 in CodeSandbox!" />
</template>

<script>
import index from "./components/index.vue";
export default {
  name: "App",
  components: {
    HelloWorld: index,
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>

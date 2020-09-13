<template>
  <div id="app">
    <main>
      <router-view></router-view>
    </main>
  </div>
</template>

<script>
export default {
  name: "app",
};
</script>

<style>
html {
  background-color: #222222;
  margin: 0;
  padding: 0;
  width: 100%;
}
main {
  padding: 2% 6%;
  text-align: center;
  font-family: "Comic Neue", cursive;
}
</style>

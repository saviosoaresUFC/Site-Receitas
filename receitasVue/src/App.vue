<script setup lang="ts">
</script>

<template>
  <div id="app">
    <nav>
      <router-link to="/">Home (Receitas)</router-link> |
      <router-link to="/sobre">Sobre</router-link>
    </nav>

    <main>
      <router-view />
    </main>
  </div>
</template>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
nav {
  padding: 30px;
}
nav a {
  font-weight: bold;
  color: #2c3e50;
  text-decoration: none;
  margin: 0 10px;
}
nav a.router-link-exact-active {
  color: #42b983;
}
</style>
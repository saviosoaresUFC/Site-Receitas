<script setup lang="ts">
import { useRouter } from 'vue-router';

const router = useRouter();
</script>

<template>
  <div class="not-found-view">
    <h1>404</h1>
    <h2>Página Não Encontrada</h2>
    <p>
      Você digitou uma URL que não existe no nosso site de receitas.
    </p>
    <button @click="router.push('/')" class="home-button">
      Voltar para a Home
    </button>
  </div>
</template>

<style scoped>
.not-found-view {
  max-width: 600px;
  margin: 80px auto;
  padding: 40px;
  text-align: center;
  border: 1px solid #ccc;
  background-color: #fcfcfc;
}
h1 {
  font-size: 5em;
  color: #e74c3c;
  margin-bottom: 0;
}
h2 {
  color: #333;
  margin-top: 5px;
}
.home-button {
  margin-top: 20px;
  padding: 10px 20px;
  background-color: #42b983;
  color: white;
  border: none;
  cursor: pointer;
}
.home-button:hover {
  background-color: #468f6c;
}
</style>
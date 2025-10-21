<script setup lang="ts"></script>

<template>
  <div class="about-view">
    <h1>Sobre o Projeto: Site de Receitas</h1>
    <p>
      Este projeto foi desenvolvido como um exercício prático para o uso do <strong>Vue Router</strong> em aplicações Vue.js 3.
    </p>
    <p>
      O principal objetivo é demonstrar a implementação de diferentes tipos de rotas:
    </p>
    <ul>
      <li><strong>Rota Estática:</strong> A página "Sobre" (`/sobre`).</li>
      <li><strong>Rota com Query Params:</strong> A página principal de listagem de receitas com paginação (`/`).</li>
      <li><strong>Rota Dinâmica:</strong> A página de detalhes de cada receita (`/receitas/:id`).</li>
    </ul>
    <p>
      Os dados das receitas são servidos por um <strong>JSON Server</strong>, simulando uma API RESTful completa.
    </p>
  </div>
</template>

<style scoped>
.about-view {
  max-width: 800px;
  margin: 0 auto;
  padding: 20px;
  text-align: left;
}
h1 {
  color: #42b983;
  margin-bottom: 20px;
  text-align: center;
}
ul {
  list-style: square;
  margin-left: 20px;
}
p {
  line-height: 1.6;
}
</style>
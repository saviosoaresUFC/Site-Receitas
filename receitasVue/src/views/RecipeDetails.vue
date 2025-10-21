<script setup lang="ts">
import { ref, onMounted, computed } from 'vue';
import { useRoute, useRouter } from 'vue-router';
import axios from 'axios';

interface FullRecipe {
    id: number;
    titulo: string;
    foto: string;
    introducao: string;
    ingredientes: string[];
    preparo: string[];
}

const recipe = ref<FullRecipe | null>(null);
const isLoading = ref(true);
const error = ref<string | null>(null);

const route = useRoute();
const router = useRouter();

const API_BASE_URL = 'http://localhost:3000';
const API_URL = 'http://localhost:3000/receitas';
const recipeId = route.params.id;

const fullImageUrl = computed(() => {
    if (recipe.value?.foto) {
        return `${API_BASE_URL}${recipe.value.foto}`;
    }
    return null;
});

// Busca da Receita
async function fetchRecipe() {
    isLoading.value = true;
    error.value = null;

    try {
        const response = await axios.get<FullRecipe>(`${API_URL}/${recipeId}`);

        if (!response.data || Object.keys(response.data).length === 0) {
            // se o JSON Server retornar 200 com corpo vazio, é direcionado para a página 404
            router.replace({ name: 'NotFound' });
            return;
        }

        recipe.value = response.data;

    } catch (err) {
        if (axios.isAxiosError(err) && err.response?.status === 404) {
            router.replace({ name: 'NotFound' });
        } else {
            console.error("Erro ao buscar detalhes da receita:", err);
            error.value = "Ocorreu um erro ao carregar os detalhes da receita.";
        }
    } finally {
        isLoading.value = false;
    }
}

// Faz a busca assim que o componente é montado
onMounted(fetchRecipe);
</script>

<template>
    <div class="recipe-details-view">
        <button @click="router.back()" class="back-button">
            &larr; Voltar para a lista
        </button>

        <div v-if="isLoading" class="loading">
            Carregando detalhes da receita...
        </div>

        <div v-else-if="error" class="error-message">
            <h2>Erro</h2>
            <p>{{ error }}</p>
        </div>

        <div v-else-if="recipe" class="recipe-content">
            <h1>{{ recipe.titulo }}</h1>

            <figure v-if="fullImageUrl" class="recipe-image-container">
                <img :src="fullImageUrl" :alt="recipe.titulo" class="recipe-image" />
            </figure>

            <section class="ingredients">
                <h2>Ingredientes</h2>
                <ul>
                    <li v-for="(item, index) in recipe.ingredientes" :key="index">
                        {{ item }}
                    </li>
                </ul>
            </section>

            <section class="preparation">
                <h2>Modo de Preparo</h2>
                <ol>
                    <li v-for="(step, index) in recipe.preparo" :key="index">{{ step }}</li>
                </ol>
            </section>
        </div>

        <div v-else>
            <p>Receita não encontrada.</p>
        </div>
    </div>
</template>

<style scoped>
.recipe-details-view {
    max-width: 800px;
    margin: 0 auto;
    padding: 20px;
    text-align: left;
}

.back-button {
    padding: 10px 15px;
    margin-bottom: 20px;
    cursor: pointer;
    background-color: #f4f4f4;
    border: 1px solid #ccc;
    border-radius: 5px;
}

.recipe-image-container {
    margin: 20px 0;
    text-align: center;
}
.recipe-image {
    max-width: 350px;
    height: auto;
    border-radius: 8px;
    box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
}
.recipe-intro {
    font-style: italic;
    margin-bottom: 30px;
    padding: 0 10px;
}

.loading,
.error-message {
    text-align: center;
    margin-top: 50px;
}

.ingredients,
.preparation {
    margin-top: 30px;
    padding: 15px;
    border-left: 5px solid #42b983;
    background-color: #f9f9f9;
}

.ingredients ul {
    list-style: disc inside;
    padding-left: 0;
}

.preparation ol {
    list-style: decimal inside;
    padding-left: 0;
}
</style>
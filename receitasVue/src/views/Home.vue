<script setup lang="ts">
import { ref, watch, computed, onMounted } from 'vue';
import { useRoute, useRouter } from 'vue-router';
import axios from 'axios';

interface Recipe {
    id: number;
    titulo: string;
}

const allRecipes = ref<Recipe[]>([]); // Lista completa das receitas
const isLoading = ref(true);
const route = useRoute();
const router = useRouter();

const API_URL = 'http://localhost:3000/receitas';
const ITEMS_PER_PAGE = 10;

// O total de receitas é o tamanho da lista completa.
const totalRecipes = computed(() => allRecipes.value.length); 

// Lê o parâmetro '_page' da URL.
const currentPage = computed(() => {
    return parseInt(route.query._page as string) || 1;
});

// Calcula o total de páginas com base na lista completa.
const totalPages = computed(() => {
    return Math.ceil(totalRecipes.value / ITEMS_PER_PAGE);
});

// Fatia a lista completa para exibir apenas as receitas da página atual.
const paginatedRecipes = computed(() => {
    const start = (currentPage.value - 1) * ITEMS_PER_PAGE;
    const end = start + ITEMS_PER_PAGE;
    
    return allRecipes.value.slice(start, end);
});

// Busca todas as receitas
async function fetchAllRecipes() {
    isLoading.value = true;
    allRecipes.value = [];

    try {
        const response = await axios.get<Recipe[]>(API_URL);

        allRecipes.value = response.data;

    } catch (error) {
        console.error("Erro ao buscar todas as receitas:", error);
    } finally {
        isLoading.value = false;
        
        // força a validação da URL após o carregamento
        validatePage(currentPage.value); 
    }
}

// Valida que o usuário não acesse páginas que não existem (ex: /?_page=999)
function validatePage(page: number) {
    if (page < 1 || page > totalPages.value) {
        if (totalPages.value > 0) {
            // se tiver páginas, redireciona para a última
            router.replace({ query: { _page: totalPages.value } });
        } else {
            // se não houver receitas, limpa a query
             router.replace({ query: {} }); 
        }
    }
}


function goToPage(page: number) {
    if (page > 0 && page <= totalPages.value) {
        // Usa o router para mudar a query
        router.push({ query: { _page: page === 1 ? undefined : page.toString() } });
    }
}

function viewRecipe(id: number) {
    router.push({ name: 'RecipeDetails', params: { id: id.toString() } });
}

// Busca os dados apenas na montagem do componente.
onMounted(fetchAllRecipes);

// Observa mudanças na query '_page' da rota para re-renderizar
watch(
    () => route.query._page,
    () => {
        validatePage(currentPage.value); 
    }
);
</script>

<template>
    <div class="home-view">
        <h1>Todas as Receitas</h1>

        <div v-if="isLoading" class="loading">
            Carregando receitas...
        </div>

        <ul v-else-if="paginatedRecipes.length" class="recipe-list">
            <li v-for="recipe in paginatedRecipes" :key="recipe.id" @click="viewRecipe(recipe.id)" class="recipe-item">
                {{ recipe.titulo }} 
            </li>
        </ul>

        <p v-else-if="!isLoading && allRecipes.length === 0">
            Não foram encontradas receitas. Verifique se o JSON Server está rodando!
        </p>
        
        <div v-if="totalPages > 1" class="pagination-controls">
            <button 
                @click="goToPage(currentPage - 1)" 
                :disabled="currentPage <= 1"
            >
                &lt; Anterior
            </button>

            <span>Página {{ currentPage }} de {{ totalPages }}</span>

            <button 
                @click="goToPage(currentPage + 1)" 
                :disabled="currentPage >= totalPages"
            >
                Próxima &gt;
            </button>
        </div>
    </div>
</template>

<style scoped>
.home-view {
    max-width: 800px;
    margin: 0 auto;
    padding: 20px;
}

.loading {
    font-style: italic;
    color: #666;
}

.recipe-list {
    list-style: none;
    padding: 0;
}

.recipe-item {
    background: #f4f4f4;
    border: 1px solid #ddd;
    padding: 15px;
    margin-bottom: 10px;
    cursor: pointer;
    text-align: left;
}

.recipe-item:hover {
    background: #e9e9e9;
}

.pagination-controls {
    margin-top: 20px;
}

.pagination-controls button {
    padding: 8px 15px;
    margin: 0 5px;
    cursor: pointer;
}
</style>
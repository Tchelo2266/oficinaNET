<script setup>
import { ref } from 'vue';

const paginas = ref([
    {
        titulo: "Início",
        rota: ""
    },
    {
        titulo: "Clientes",
        rota: "cliente"
    },
    {
        titulo: "Veículos",
        rota: "veiculo"
    },
    {
        titulo: "Servicos",
        rota: "servico"
    },
    {
        titulo: "Tipo de serviço",
        rota: "tipoServico"
    }
])


</script>

<template>
    <nav class="navbar navbar-expand-lg bg-dark border-bottom border-body" data-bs-theme="dark">
        <div class="container-fluid justify-content-start">
            <a class="navbar-brand position-absolute nome-projeto" href="/">OficinaNet</a>
            <!-- <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarNav" aria-controls="navbarNav" aria-expanded="false" aria-label="Toggle navigation">
                <span class="navbar-toggler-icon"></span>
            </button> -->
            <div class="d-flex text-light mx-auto my-2">
                <div class="px-4" v-for="pagina in paginas">
                    <NuxtLink class="nav-link menu-link" :to="'/'+pagina.rota">{{ pagina.titulo }}</NuxtLink>
                </div>
            </div>
            <!-- <div class="collapse navbar-collapse justify-content-center" id="navbarNav">
            </div> -->
        </div>
    </nav>
</template>

<style>

.menu-link{
    font-size: 1.2rem;
}

.nome-projeto{
    font-size: 1.5rem;
}

</style>
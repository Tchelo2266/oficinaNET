<script setup>
import { onMounted } from "vue"

const baseUrl = "https://localhost:7294/tipoServico";

// Variaveis de controle
const editando = ref(false);

// Listas
const listaTipoServico = ref();

// Objetos
const objTipoServico = ref({});

onMounted(async () => {
    await buscaTipoServico();
})

async function buscaTipoServico() {
    let response = await $fetch(baseUrl);
    listaTipoServico.value = response;
}


async function salvar(){
    if(editando.value){
        await $fetch(baseUrl + "/"+ objTipoServico.value.id, {method: "PUT", body: objTipoServico.value});
        editando.value = false;
    }else{
        objTipoServico.value.quemCadastrou = 1;
        await $fetch(baseUrl, {method: "POST", body: objTipoServico.value});
        objTipoServico.value = {};
    }
    await buscaTipoServico();
}

function editar(clienteId) {
    objTipoServico.value = listaTipoServico.value.find(item => item.id == clienteId);
    editando.value = true;
}

async function excluir(tipoServicoId) {
    await $fetch(baseUrl + "/"+ tipoServicoId, {method: "DELETE"});
    await buscaTipoServico();
    editando.value = false;
}

function cancelar() {
    objTipoServico.value = {};
    editando.value = false;
}

</script>

<template>
    <div>
        <div class="container-fluid">
            <div class="row">
                <div class="col-3">
                    <h1 class="titulo-pagina">Tipo de serviço</h1>
                    <hr class="ms-3 mt-1">
                </div>
            </div>
            <div class="row ms-1">
                <div class="col-6">
                    <div class="formulario">
                        <div class="row mb-3">
                            <div class="col-md-5 col-sm-10">
                                <label class="form-label" for="descricao">Descrição</label>
                                <input class="form-control" type="text" name="descricao" id="descricao" v-model="objTipoServico.descricao"/>
                            </div>
                        </div>
                        <div class="row">
                            <div class="col-md-3 col-sm-3">
                                <div v-if="!editando">
                                    <button class="btn btn-success" @click="salvar"><Icon name="ic:baseline-check" color="white" style="font-size: large;" />Salvar</button>
                                </div>
                                <div v-else>
                                    <button class="btn btn-warning me-3" @click="salvar"><Icon name="ic:baseline-check" color="black" style="font-size: large;" />Alterar</button>
                                    <button class="btn btn-danger" @click="cancelar"><Icon name="ic:round-close" color="white" style="font-size: large;" />Cancelar</button>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
                <div class="col-6">
                    <div class="tabela">
                        <table class="table table-striped table-bordered">
                            <thead>
                                <tr>
                                    <th>Descerição</th>
                                    <th></th>
                                </tr>
                            </thead>
                            <tbody>
                                <tr v-for="tipoServico in listaTipoServico">
                                    <td>{{ tipoServico.descricao }}</td>
                                    <td>
                                        <button class="btn btn-sm" @click="editar(tipoServico.id)">
                                            <Icon name="ic:baseline-edit" color="black" style="font-size: large;" />
                                        </button>
                                        <button class="btn btn-sm" @click="excluir(tipoServico.id)">
                                            <Icon name="ic:baseline-delete-forever" color="red" style="font-size: large;" />
                                        </button>
                                    </td>
                                </tr>
                            </tbody>
                        </table>

                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<style>

.titulo-pagina {
    margin: 20px 0 0 20px;
}

</style>
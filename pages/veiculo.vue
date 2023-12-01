<script setup>
import { onMounted } from "vue"

const baseUrlVeiculo = "https://localhost:7294/veiculo";
const baseUrlCliente = "https://localhost:7294/cliente";

// Variaveis de controle
const editando = ref(false);

// Listas
const listaCliente = ref([]);
const listaVeiculo = ref([]);

// Objetos
const objVeiculo = ref({});
const cliente = ref(0);

onMounted(async () => {
    listaCliente.value = await $fetch(baseUrlCliente);    
})

async function buscaVeiculos() {
    listaVeiculo.value = await $fetch(`${baseUrlVeiculo}/cliente/${cliente.value}`);
}

function editar(veiculoId) {
    let veiculo = listaVeiculo.value.find(item => item.id == veiculoId);
    for (const key in veiculo) {
        objVeiculo.value[key] = veiculo[key];
    }
    editando.value = true;
}

async function salvar(){
    if(editando.value){
        await $fetch(baseUrlVeiculo, {method: "PUT", body: objVeiculo.value});
        await buscaVeiculos();
        objVeiculo.value = {};
        editando.value = false;
    }else{
        objVeiculo.value.quemCadastrou = 1;
        objVeiculo.value.clienteId = cliente.value;
        let response = await $fetch(baseUrlVeiculo, {method: "POST", body: objVeiculo.value});
        if(response.id > 0){
            listaVeiculo.value.push(response);
        }
        console.log(response);
        objVeiculo.value = {};
    }
}

</script>

<template>
    <div class="container-fluid">
        <div class="row">
            <div class="col-3">
                <h1 class="titulo-pagina">Veículos</h1>
                <hr class="ms-3 mt-1">
            </div>
        </div>
        <div class="row ms-1">
            <div class="col-6">
                <div class="formulario">
                    <div class="row mb-3">
                        <div class="col-md-5 col-sm-10">
                            <label class="form-label" for="descricao">Cliente</label>
                            <select class="form-select" name="cliente" id="cliente" v-model="cliente" @change="buscaVeiculos">
                                <option value="0">Selecione</option>
                                <option v-for="item in listaCliente" :key="item.id" :value="item.id">{{ item.nome }}</option>
                            </select>
                        </div>
                        <div class="col-md-5 col-sm-10">
                            <label class="form-label" for="marca">Marca</label>
                            <input class="form-control" type="text" name="marca" id="marca" v-model="objVeiculo.marca"/>
                        </div>
                        <div class="col-md-5 col-sm-10">
                            <label class="form-label" for="modelo">Modelo</label>
                            <input class="form-control" type="text" name="modelo" id="modelo" v-model="objVeiculo.modelo"/>
                        </div>
                        <div class="col-md-5 col-sm-10">
                            <label class="form-label" for="placa">Placa</label>
                            <input class="form-control" type="text" name="placa" id="placa" v-model="objVeiculo.placa"/>
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
                                <th>Modelo</th>
                                <th>Placa</th>
                                <th></th>
                            </tr>
                        </thead>
                        <tbody>
                            <tr v-if="listaVeiculo.length == 0">
                                <td colspan="3">Nenhum registro encontrado</td>
                            </tr>
                            <tr v-else v-for="tipoServico in listaVeiculo">
                                <td>{{ tipoServico.modelo }}</td>
                                <td>{{ tipoServico.placa }}</td>
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
</template>
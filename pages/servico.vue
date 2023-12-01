<script setup>
import { onMounted } from "vue"

const baseUrlServico = "https://localhost:7294/servico";
const baseUrlVeiculo = "https://localhost:7294/veiculo";
const baseUrlCliente = "https://localhost:7294/cliente";
const baseUrlTipoServico = "https://localhost:7294/tipoServico";

// Variaveis de controle
const editando = ref(false);

// Listas
const listaServico = ref([]);
const listaTipoServico = ref([]);
const listaCliente = ref([]);
const listaVeiculo = ref([]);

// Objetos
const objServico = ref({});
// const cliente = ref(0);
// const veiculo = ref();
// const tipoServico = ref();

onMounted(async () => {
    listaTipoServico.value = await $fetch(baseUrlTipoServico);
    listaCliente.value = await $fetch(baseUrlCliente);    
})

async function buscaVeiculos() {
    listaVeiculo.value = await $fetch(`${baseUrlVeiculo}/cliente/${objServico.value.clienteId}`);
}

async function buscaServicos() {
    objServico.value.tipoServicoId = 0;
    objServico.value.descricao = "";
    listaServico.value = await $fetch(`${baseUrlServico}/cliente/${objServico.value.clienteId}/veiculo/${objServico.value.veiculoId}`);
    console.log(listaServico.value);
}

function editar(servico) {
    editando.value = true;;
    objServico.value = copiaObj(servico);
}

async function excluir(servico) {
    await $fetch(`${baseUrlServico}/${servico.id}`, {method: "DELETE"});
    await buscaServicos();
}


async function salvar() {
    let data = copiaObj(objServico.value);
    let metodo = editando.value ? "PUT" : "POST";

    await $fetch(baseUrlServico, {method: metodo, body: data});
    await buscaServicos();
}

function formataData(dataSemFormato) {
    let dia, mes, ano;
    let data = new Date(dataSemFormato);
    dia = data.getDate().toString().padStart(2, 0);
    mes = (data.getMonth() + 1).toString().padStart(2, 0);
    ano = data.getFullYear();
    return `${dia}/${mes}/${ano}`;
}

function copiaObj(obj) {
    let copia = {};
    for (const key in obj) {
        copia[key] = obj[key];
    }
    return copia;
}

</script>

<template>
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
                    <div class="row">
                        <div class="col-md-5 col-sm-10">
                            <label class="form-label" for="descricao">Cliente</label>
                            <select class="form-select" name="cliente" id="cliente" v-model="objServico.clienteId" @change="buscaVeiculos">
                                <option value="0">Selecione</option>
                                <option v-for="item in listaCliente" :key="item.id" :value="item.id">{{ item.nome }}</option>
                            </select>
                        </div>
                        <div class="col-md-5 col-sm-10">
                            <label class="form-label" for="descricao">Veiculo</label>
                            <select class="form-select" name="veiculo" id="veiculo" v-model="objServico.veiculoId" @change="buscaServicos" :disabled="!objServico.clienteId">
                                <option value="0">Selecione</option>
                                <option v-for="(item, index) in listaVeiculo" :key="index" :value="item.id">{{ item.modelo }} - {{ item.placa }}</option>
                            </select>
                        </div>
                        <div class="col-md-5 col-sm-10">
                            <label class="form-label" for="descricao">Tipo serviço</label>
                            <select class="form-select" name="tipoServico" id="tipoServico" v-model="objServico.tipoServicoId">
                                <option value="0">Selecione</option>
                                <option v-for="(item, index) in listaTipoServico" :key="index" :value="item.id">{{ item.descricao }}</option>
                            </select>
                        </div>
                    </div>
                    <div class="row mb-3">
                        <div class="col-md-10 col-sm-10">
                            <label class="form-label" for="descricao">Descricao</label>
                            <textarea class="form-control" type="text" name="descricao" id="descricao" v-model="objServico.descricao"/>
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
                                <th>Data</th>
                                <th></th>
                            </tr>
                        </thead>
                        <tbody>
                            <tr v-if="listaServico.length == 0">
                                <td colspan="3">Nenhum registro encontrado</td>
                            </tr>
                            <tr v-for="servico in listaServico">
                                <td>{{ servico.descricao }}</td>
                                <td>{{ formataData(servico.dataCadastro) }}</td>
                                <td>
                                    <button class="btn btn-sm" @click="editar(servico)">
                                        <Icon name="ic:baseline-edit" color="black" style="font-size: large;" />
                                    </button>
                                    <button class="btn btn-sm" @click="excluir(servico)">
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
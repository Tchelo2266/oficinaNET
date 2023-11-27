<script setup>
import { onMounted } from "vue"

// Variaveis de controle
const editando = ref(false);

// Listas
const listaClientes = ref();

// Objetos
const objCliente = ref({});

onMounted(async () => {
    let response = await $fetch("https://localhost:7294/cliente");
    console.log(response);

    listaClientes.value = response;
})


async function salvar(){
    if(editando.value){
        let response = await $fetch("https://localhost:7294/cliente/"+ objCliente.value.id, {method: "POST", body: objCliente.value});
        if(response.id){
            let idx = listaClientes.value.findIndex(item => item.id == objCliente.value.id)
            listaClientes.value[idx] = response;
            objCliente.value = {};
            editando.value = false;
        }
    }else{
        objCliente.value.quemCadastrou = 1;
        let response = await $fetch("https://localhost:7294/cliente", {method: "POST", body: objCliente.value});
        if(response.id > 0){
            listaClientes.value.push(response);
        }
        console.log(response);
        objCliente.value = {};
    }
}

function editar(clienteId) {
    objCliente.value = listaClientes.value.find(item => item.id == clienteId);
    editando.value = true;
}

function excluir(clienteId) {
    console.log("excluir");
}

function cancelar() {
    objCliente.value = {};
    editando.value = false;
}

</script>

<template>
    <div>
        <div class="container-fluid">
            <div class="row">
                <div class="col-2">
                    <h1 class="titulo-pagina">Clientes</h1>
                    <hr class="ms-3 mt-1">
                </div>
            </div>
            <div class="row ms-1">
                <div class="col-6">
                    <div class="formulario">
                        <div class="row mb-3">
                            <div class="col-md-5 col-sm-10">
                                <label class="form-label" for="nome">Nome</label>
                                <input class="form-control" type="text" name="nome" id="nome" v-model="objCliente.nome"/>
                            </div>
                            <div class="col-md-5 col-sm-10 ">
                                <label class="form-label" for="telefone">Telefone</label>
                                <input class="form-control" type="text" name="telefone" id="telefone" v-model="objCliente.telefone"/>
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
                                    <th>Nome</th>
                                    <th>Telefone</th>
                                    <th></th>
                                </tr>
                            </thead>
                            <tbody>
                                <tr v-for="cliente in listaClientes">
                                    <td>{{ cliente.nome }}</td>
                                    <td>{{ cliente.telefone }}</td>
                                    <td>
                                        <button class="btn btn-sm" @click="editar(cliente.id)">
                                            <Icon name="ic:baseline-edit" color="black" style="font-size: large;" />
                                        </button>
                                        <button class="btn btn-sm" @click="excluir(cliente.id)">
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
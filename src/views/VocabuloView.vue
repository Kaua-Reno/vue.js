<script setup lang="ts">
    import { ref, onMounted } from 'vue'
    import axios from 'axios'

    interface Entrega {
        id: number,
        descricao: string,
        dataHoraCadastro: string,
        dataHoraLimite: string,
        peso: number,
        observacoes: string
    }

    const descricao = ref()
    const dataHoraCadastro = ref()
    const dataHoraLimite = ref()
    const peso = ref()
    const observacoes = ref()

    const descricaoPesquisa = ref()

    const entregas = ref([] as Entrega[])

    async function buscarEntregas() {
        entregas.value = (await axios.get("entrega")).data
    }

    async function cadastrarEntrega() {
        await axios.post("entrega", 
        {
          descricao: descricao.value,
          dataHoraCadastro: dataHoraCadastro.value,
          dataHoraLimite: dataHoraLimite.value,
          peso: peso.value,
          observacoes: observacoes.value
        });
        descricao.value = ''
        peso.value = ''
        dataHoraCadastro.value = ''
        dataHoraLimite.value = ''
        observacoes.value = ''
        buscarEntregas()
    }

    async function pesquisarEntregas() {
        entregas.value = (await axios.get("entrega/" + descricaoPesquisa.value)).data
        console.log(entregas.value)
    }

    onMounted(() => {
        buscarEntregas()
    })
</script>

<template>
    <br />
    <div class="entregas">
        <h1>Gerenciamento de Entregas</h1><br/>
        <input type="datetime-local" v-model="descricaoPesquisa" />
        <input type="submit" @click="pesquisarEntregas()" />
        <table v-if="entregas.length > 0">
            <thead>
                <td>Id</td>
                <td>Descrição</td>
                <td>Data de Cadastro</td>
                <td>Data Limite</td>
                <td>Peso</td>
                <td>Observações</td>
            </thead>
            <tbody>
                <tr v-for="entrega in entregas" :key="entrega.id">
                <td>{{ entrega.id }}</td>
                <td>{{ entrega.descricao }}</td>
                <td>{{ entrega.dataHoraCadastro }}</td>
                <td>{{ entrega.dataHoraLimite }}</td>
                <td>{{ entrega.peso }}</td>
                <td v-if="entrega.observacoes != null">{{ entrega.observacoes }}</td>
                <td v-else>Sem Observações</td>
                </tr>
            </tbody>
        </table>
        <p v-else>Nenhum registro foi encontrado para os critérios fornecidos</p>
        <br/>
        <h2>Cadastro de Entrega</h2>
        <label>Descrição:</label><br/>
        <input type="text" v-model="descricao" /><br/>
        <label>Data Hora Cadastro:</label><br/>
        <input type="datetime-local" v-model="dataHoraCadastro"><br/>
        <label>Data Hora Desativação:</label><br/>
        <input type="datetime-local" v-model="dataHoraLimite"><br/>
        <label>Peso:</label><br/>
        <input type="number" v-model="peso"/><br/>
        <label>Observações:</label><br/>
        <input type="text" v-model="observacoes"/><br/>
        <input type="submit" value="Cadastrar Vocábulo" @click="cadastrarEntrega()"/><br/><br/>
    </div>
</template>

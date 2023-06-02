<template>
  <main class="app container" style="width: auto; max-width: 600px;">
    <div class="apresentacao p-2">
      <h2 class="titulo">
        Olá, <input type="text" placeholder="Digite seu nome" v-model="nome" style="color: #212121;"/>
      </h2>
    </div>
    <div class="criar-lista p-2">

      <h4 class="text-dark">Criar Lista de Tarefas</h4>
      <form @submit.prevent="addLista">

        <h6>O que deseja adicionar à lista de tarefas?</h6>
        <input type="text" placeholder="E.x. Comprar café" v-model="entrada_conteudo" class="form-control"/>
        
        <h6>Selecione a categoria</h6>
        <div class="categorias">

          <label>
            <input type="radio" value="trabalho" name="categorias" id="categoria1" v-model="entrada_tipo">
            <span class="circulo trabalho"></span>
            <div>Trabalho</div>
          </label>

          <label>
            <input type="radio" value="pessoal" name="categorias" id="categoria2" v-model="entrada_tipo">
            <span class="circulo pessoal"></span>
            <div>Pessoal</div>
          </label>
        </div>

        <input class="text-dark" type="submit" value="Adicionar tarefa"/>
      </form>
    </div>
    <div class="lista_tarefas p-2">
      <h4 class="text-dark">Lista de Tarefas</h4>
      <div>
        <div class="py-2 tarefas my-3" v-for="tarefa in lista_ordenada" :class="`tarefa-item ${tarefa.feito && 'feito'}`">
    
          <label>
            <input type="checkbox" v-model="tarefa.feito"/>
            <span :class="`circulo ${tarefa.tipo}`"></span>
          </label>
        
          <div class="lista-conteudo">
            <input type="text" v-model="tarefa.conteudo" disabled style="background-color: white; border: none;"/>
          </div>

          <div class="opcao me-3">
            <button @click="removeTarefa(tarefa)"><img src="./assets/trash-can-solid.svg"/></button>
          </div>
        </div>
      </div>
    </div>
  </main>
</template>

<script>
import {ref, onMounted, computed, watch} from 'vue'

export default{
  name: 'Home',
  setup(){
    const lista = ref([])
    const nome = ref('')
    const entrada_conteudo = ref('')
    const entrada_tipo = ref(null)

    const lista_ordenada = computed(() => lista.value.sort((a,b) => {
      return a.criadoEm - b.criadoEm
    }))

    watch(nome, (newVal) => {
      localStorage.setItem('nome', newVal)
    })

    watch(lista, newVal => {
      localStorage.setItem('lista', JSON.stringify(newVal))
    }, {deep: true})

    onMounted(() => {
      nome.value = localStorage.getItem('nome') || ''
      lista.value = JSON.parse(localStorage.getItem('lista')) || []
    })

    const addLista = () => {
      if (entrada_conteudo.value.trim() ==='' || entrada_tipo.value === null){
        return
      }
      lista.value.push({
        conteudo: entrada_conteudo.value,
        tipo: entrada_tipo.value,
        feito: false,
        criadoEm: new Date().getTime()
      })
      entrada_conteudo.value = ''
      entrada_tipo.value = null
    }

    const removeTarefa = (tarefa) => {
      lista.value = lista.value.filter((t) => t !== tarefa)
    }
    
    return{ nome, lista, entrada_conteudo, entrada_tipo, lista_ordenada, addLista, removeTarefa}
  }
}

</script>

<style scoped>

</style>
<template>
<div>
  <div class="row col-xs-12 col-md-12 col-xl-12">
    <cp-tabela-listagem :titulo="'Painel de Cursos'" :campos="fields" :items="items"></cp-tabela-listagem>
  </div>
</div>
</template>

<script>
import CpTabelaListagem from '../components/TabelaListagem'
import { QInput, QSelect, QModal } from 'quasar'
import EventBus from '../services/EventBus'

export default {
  name: 'PageIndex',
  components: {
    CpTabelaListagem,
    QInput,
    QModal,
    QSelect
  },
  data () {
    return {
      show: false,
      name: undefined,
      fields: [
        {nome: 'Empresa'},
        {nome: 'Nome do curso'},
        {nome: 'Descrição'},
        {nome: 'Quantidade de alunos'},
        {nome: 'Edição de conteudo'}
      ],
      items: []
    }
  },
  methods: {
    open () {
      this.show = true
    },
    close () {
      this.show = false
    },
    getFields () {
      this.$http.get('https://localhost:5001/api/course').then(response => {
        debugger
        this.items = response.body.sort((a, b) => a.enterprise !== b.enterprise ? a.enterprise < b.enterprise ? -1 : 1 : 0)
      })
    }
  },
  mounted () {
    this.getFields()
    EventBus.$on('getFields', () => this.getFields())
    EventBus.$on('getList', () => this.getFields())
  }
}
</script>
<style>
</style>

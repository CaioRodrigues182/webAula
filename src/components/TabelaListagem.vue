<template>
  <div class="container-courses">
  <div class="row">
    <div class="col-xs-12 col-md-3 col-xl-3">
      <h5>{{ titulo }}</h5>
    </div>
      <div class="row offset-xl-3 offset-xs-1">
        <div class="filters row">
          <div class="col-xs-4 col-md-4 col-xl-4">
            <q-select v-model="order" :options="optionsCharged" float-label="Ordenar por" />
          </div>&nbsp;&nbsp;&nbsp;&nbsp;
          <div class="col-xs-7 col-md-7 col-xl-7">
            <q-input v-model="search" @blur="getFields" float-label="Pesquisar cursos ou empresas" />
          </div>&nbsp;&nbsp;&nbsp;&nbsp;
        </div>
        <div class="row btn-add">
          <div class="offset-xs-3 col-xs-12 col-md-12 col-xl-12">
            <q-btn class="btn-list-add" @click="open"> Adicionar cursos
            <q-icon name="add_box" />
            </q-btn>
          </div>
        </div>
      </div>
    </div>
    <div class="row">
      <div style="overflow-x:auto;" class="col-xs-12 col-md-12 col-xl-12">
        <table class="courses">
          <thead>
            <tr>
              <th class="col-grid" v-for="campo in campos" :key="campo.nome">
                  {{campo.nome}}
              </th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="item in registrosFiltrados" :key="item.nome">
              <td>
                {{item.enterprise}}
              </td>
              <td>
                {{item.name}}
              </td>
              <td>
                {{item.description}}
              </td>
              <td>
                {{item.quantity}}
              </td>
              <td >
                <div class="row">
                  <q-btn class="btn-edit col-xl-5 btn-list btn-list-edit" small @click="editFields(item)" size="sm">Editar</q-btn>
                  <q-btn class="btn-delete col-xl-5 btn-list btn-list btn-list-danger" @click="openExclude(item)" size="sm">Excluir</q-btn>
                </div>
              </td>
              <hr>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
  <q-modal v-model="insert" @close="insert = false">
    <div class="padding-modal">
      <h6>{{ title + 'curso'}}</h6>
    </div>
    <q-modal-layout>
      <div class="modal-layout">
          <div class="padding-modal col-xs-4 col-md-4 col-xl-4">
            <q-select v-model="course.company" :options="selectOptions" float-label="Empresas" />
          </div>
            <div class="padding-modal col-xs-4 col-md-4 col-xl-4">
            <q-input name="nome" v-model="course.name" float-label="Nome do curso" />
          </div>
            <div class="padding-modal col-xs-4 col-md-4 col-xl-4">
            <q-input v-model="course.quantity" name="qtd" float-label="Quantidade de alunos" />
          </div>
            <div class="padding-modal col-xs-4 col-md-4 col-xl-4">
            <q-input type="textarea" rows="10" v-model="course.description" float-label="Descrição" />
          </div>
      </div>
      <div class="padding-btn row">
        <div class="offset-xs-1 offset-xl-6">
          <div v-if="title == 'Cadastrar novo '">
            <q-btn  class="btn-list-edit" @click="addCourse" label="Salvar" />
          </div>
          <div v-else>
            <q-btn class="btn-list-edit" @click="updateCourse" label="Editar" />
          </div>
        </div>
          <div>
            <q-btn color="secondary" @click="close" label="Fechar" />
          </div>
      </div>
    </q-modal-layout>
  </q-modal>

  <q-modal v-model="modalExclude" @close="modalExclude = false">
    <div class="offset-12 padding-btn row">
    <div class="exclude-modal">
      <span>Tem certeza que deseja excluir?</span>
        &nbsp;&nbsp;&nbsp;
          <q-btn size="sm" color="red" @click="deleteCourse" label="Excluir" />
          <q-btn size="sm" color="secondary"  @click="closeExclude" label="Fechar" />
        &nbsp;&nbsp;&nbsp;
    </div>
      </div>
  </q-modal>
  </div>
</template>

<script>
import { QModal, Notify, QModalLayout, QSelect, QToolbar, QInput } from 'quasar'
import EventBus from '../services/EventBus'

export default {
  name: 'TabelaListagem',
  components: {
    QModal,
    QModalLayout,
    QToolbar,
    QInput,
    QSelect,
    Notify
  },
  props: {
    titulo: String,
    campos: {
      type: Array,
      required: true
    },
    items: {
      type: Array,
      required: true
    }
  },
  computed: {
    optionsCharged () {
      return this.filtros
    }
  },
  watch: {
    items () {
      this.registrosFiltrados = this.items
    },
    search: {
      handler (val) {
        let regexName = null
        regexName = new RegExp(`^.*${val}.*$`, 'i')
        this.registrosFiltrados = this.registrosFiltrados.filter((registro) => {
          if (regexName && (!regexName.test(registro.name) && !regexName.test(registro.enterprise))) {
            return false
          }
          return true
        })
      }
    }
  },
  data () {
    return {
      title: undefined,
      order: undefined,
      search: undefined,
      oldEnterprise: undefined,
      modalExclude: false,
      course: {
        name: undefined,
        quantity: undefined,
        description: undefined,
        company: undefined
      },
      forRemove: undefined,
      registrosFiltrados: [],
      insert: false,
      selectOptions: [],
      filtros: [
        {
          label: 'Empresa',
          value: 'empresa'
        },
        {
          label: 'Maior qtd alunos',
          value: 'qtdMaior'
        },
        {
          label: 'Menor qtd alunos',
          value: 'qtdMenor'
        },
        {
          label: 'Mais recentes',
          value: 'recentes'
        },
        {
          label: 'Mais antigos',
          value: 'antigos'
        }
      ]
    }
  },
  methods: {
    open () {
      this.clear()
      this.insert = true
      this.title = 'Cadastrar novo '
    },
    close () {
      this.insert = false
    },
    openExclude (item) {
      this.modalExclude = true
      this.forRemove = {
        id_company: item.enterpriseId,
        id_course: item.id
      }
    },
    closeExclude () {
      this.modalExclude = false
    },
    getFields () {
      EventBus.$emit('getFields')
    },
    editFields (course) {
      this.oldEnterprise = course.enterpriseId
      this.insert = true
      this.title = 'Editar '
      this.course = {
        id: course.id,
        company: course.enterpriseId,
        quantity: course.quantity,
        name: course.name,
        description: course.description
      }
    },
    updateCourse () {
      if (!this.course.name && !this.course.quantity) {
        Notify.create({color: 'yellow', icon: 'cancel', message: 'Preencha a empresa, o nome e a quantidade'})
      } else {
        const params = {
          id: this.course.id,
          EnterpriseId: this.course.company,
          Enterprise: this.course.name,
          Status: 'Active',
          Name: this.course.name,
          Quantity: this.course.quantity,
          Description: this.course.description
        }
        return this.$http.put(`https://localhost:5001/api/course/${this.oldEnterprise}/${this.course.id}`, params, {
          headers: {
            'Content-Type': 'application/json'
          }}).then((response) => {
          this.insert = false
          Notify.create({color: 'blue', icon: 'check', message: 'Operação realizada com sucesso!'})
          EventBus.$emit('getList')
        })
          .catch(() => Notify.create({color: 'red', icon: 'cancel', message: 'Erro ao realizar operação!'}))
      }
    },
    deleteCourse () {
      return this.$http.delete(`https://localhost:5001/api/course/${this.forRemove.id_company}/${this.forRemove.id_course}`)
        .then((response) => {
          this.modalExclude = false
          Notify.create({color: 'blue', icon: 'check', message: 'Operação realizada com sucesso!'})
          EventBus.$emit('getList')
        })
        .catch(() => Notify.create({color: 'red', icon: 'cancel', message: 'Erro ao realizar operação!'}))
    },
    getCompany () {
      this.$http.get('https://localhost:5001/api/enterprise').then((response) => {
        let array = []
        for (const iterator of response.body) {
          array.push({ label: iterator.enterprise, value: iterator.enterpriseId })
        }
        this.selectOptions = array.filter(function (a) {
          return !this[JSON.stringify(a)] && (this[JSON.stringify(a)] = true)
        }, Object.create(null))
        EventBus.$emit('getList')
      })
    },
    clear () {
      this.course = {
        name: undefined,
        company: undefined,
        quantity: undefined,
        description: undefined
      }
    },
    addCourse () {
      if (!this.course.name && !this.course.quantity) {
        Notify.create({color: 'yellow', icon: 'cancel', message: 'Preencha a empresa, o nome e a quantidade'})
      } else {
        let id
        for (const iterator of this.registrosFiltrados) {
          id = iterator.id
        }
        const params = {
          Id: id + 1,
          EnterpriseId: this.course.company,
          Enterprise: this.course.name,
          Status: 'Active',
          Name: this.course.name,
          Quantity: this.course.quantity,
          Description: this.course.description
        }
        this.$http.post('https://localhost:5001/api/course', params, {
          headers: {
            'Content-Type': 'application/json'
          }}).then((response) => {
          this.insert = false
          Notify.create({color: 'blue', icon: 'check', message: 'Operação realizada com sucesso!'})
          EventBus.$emit('getList')
        })
          .catch(() => Notify.create({color: 'red', icon: 'cancel', message: 'Erro ao realizar operação!'}))
      }
    }
  },
  mounted () {
    this.getCompany()
  }
}
</script>

<style scoped>
.ponteiro {
  cursor:pointer;
}
th, td {
  text-align: center;
}
.table_item:hover {
  background-color: #E9E9E9;
}
.table{
  border-collapse: collapse;
  border-bottom: 1px dashed black;
  margin-bottom: 0 !important;
}
.courses {
    font-family: "Trebuchet MS", Arial, Helvetica, sans-serif;
    border-collapse: collapse;
    width: 100%;
    color: #747474;
}
.col-grid {
  min-width: 150px;
}
.courses th {
    padding-top: 12px;
    padding-bottom: 12px;
    text-align: left;
    background-color: #ffff;
    color: #7B96C5;
}

.courses td, #customers th {
    border: 1px solid #ddd;
    padding: 8px;
}

.courses tr {
  height: 80px;
}
.courses tr:nth-child(even){background-color: #f2f2f2;}

.courses tr:hover {background-color: #ddd;}

.container-courses {
  padding: 10px;
  width: 100%;
}
.filters {
  padding-top: 22px;
}
.modal-layout {
  width: 500px;
}
@media only screen and (min-width: 350px) {
  .modal-layout {
    width: 380px;
    overflow-y: hidden;
  }
  body { overflow-x: hidden; }
}
.padding-modal {
  padding-left: 35px;
  padding-right: 70px;
}

.btn-add {
  margin-top: 40px;
}

.padding-btn {
  padding-top: 20px;
  padding-left: 8px;
  padding-bottom: 20px;
}
.exclude-modal {
  width: 100%;
  height: 30px;
}
.exclude-modal span {
  font-size: 15px;
}
.btn-list {
  width: 65px;
  height: 32px;
  background-color: #F3F4F8;
  color: #7B96C5;
}
.btn-list-edit:hover {
  background-color: #7B96C5;
  color: #fff;
}
.btn-list-danger {
  border-radius: 0 15px 15px 0;
}
.btn-list-danger:hover {
  background-color: #7B96C5;
  color: #fff;
}
.btn-list-edit {
  border-radius: 15px 0 0 10px;
}
.btn-list-add {
  border-radius: 20px !important;
  color: white;
  border-radius: 0%;
  background: #26D3BF;
}
.table-padding {
  padding: 10px;
}
</style>

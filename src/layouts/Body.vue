<template>
  <q-layout view="lHh Lpr lFf">
    <q-layout-header>
      <q-toolbar
        class="toolbar-web"
        :glossy="$q.theme === 'mat'"
        :inverted="$q.theme === 'ios'">
        <q-btn
          flat
          dense
          round
          @click="leftDrawerOpen = !leftDrawerOpen"
          aria-label="Menu">
          <q-icon class="icon-menu" size="30px" name="menu" />
        </q-btn>
        <div class="offset-xs-6 offset-xl-10 row">
          <div>
            <img src="../../img/avatar.png" height="40" width="40">
          </div>
         <div class="margin-user">
          <span class="name-user">
            Joao da silva
           </span>
         </div>
        </div>
      </q-toolbar>
    </q-layout-header>

    <q-layout-drawer
      v-model="leftDrawerOpen"
      :content-class="$q.theme === 'mat' ? 'bg-grey-2' : null">
      <q-list
        no-border
        inset-delimiter>
        <q-list-header>
          <div>
            <img src="../../img/web.png" height="80" width="270">
          </div>
        </q-list-header>
        <q-item>
          <q-item-side icon="date_range" />
          <q-item-main class="item" label="Grupos colaborativos" />
        </q-item>
        <q-item>
          <q-item-side icon="folder_open" />
          <q-item-main class="item"  label="Central de Mensagens" />
        </q-item>
        <q-collapsible class="item-collapsible" indent icon="show_chart" label="Cursos em Destaque" opened>
          <div class="sub-item"> Relatórios </div>
          <div class="sub-item"> Painel de Cursos </div>
          <div class="sub-item"> Categorias </div>
        </q-collapsible>
        <q-item>
          <q-item-side icon="settings" />
          <q-item-main class="item" label="Configurações" />
        </q-item>
      </q-list>
    </q-layout-drawer>

    <q-page-container>
      <router-view />
    </q-page-container>
  </q-layout>
</template>

<script>
import { openURL, QCollapsible } from 'quasar'

export default {
  name: 'Body',
  data () {
    return {
      leftDrawerOpen: this.$q.platform.is.desktop
    }
  },
  methods: {
    openURL
  },
  components: {
    QCollapsible
  }
}
</script>
<style>
.bg-primary {
  background: #F2F3F8 !important;
}
.toolbar-web {
  height: 110px;
}
.name-user {
  text-overflow: ellipsis;
  float: right;
  position: absolute;
  color: black;
  font-size: 15px;
  padding-bottom: 10px;
  color: #7B96C5;
}
.icon-menu {
  color: #7B96C5;
}
.q-list-header {
  margin-bottom: 20px;
}
.bg-grey-2 {
  color: rgba(223, 213, 213, 0.932);
  background: #2A3A5B !important;
}
.item:hover {
  color: #229488;
}
.item-collapsible:hover {
  color: #229488;
}
.sub-item {
  padding: 10px;
  color: #8991a2fc;
  border-left: solid 2px #229488;
}
.sub-item:hover {
  color: #fff !important;
}
.margin-user {
  margin-top: 10px;
}
.floating-name {
  margin-bottom: 15px;
}

</style>

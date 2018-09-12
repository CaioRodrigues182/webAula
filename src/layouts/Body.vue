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
          <q-icon color="black" name="menu" />
        </q-btn>
        <div class="offset-xs-6 offset-xl-9 row">
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
        link
        inset-delimiter>
        <q-list-header>
          <div>
            <img src="../../img/web.png" height="80" width="270">
          </div>
        </q-list-header>
        <q-item>
          <q-item-side icon="school" />
          <q-item-main label="Grupos colaborativos" />
        </q-item>
        <q-item>
          <q-item-side icon="code" />
          <q-item-main label="Central de mensagens" />
        </q-item>
        <q-item>
          <q-item-side icon="chat" />
          <q-item-main label="Cursos em destaques" />
        </q-item>
        <q-item>
          <q-item-side icon="record_voice_over" />
          <q-item-main label="Configurações" />
        </q-item>
      </q-list>
    </q-layout-drawer>

    <q-page-container>
      <router-view />
    </q-page-container>
  </q-layout>
</template>

<script>
import { openURL } from 'quasar'

export default {
  name: 'MyLayout',
  data () {
    return {
      leftDrawerOpen: this.$q.platform.is.desktop
    }
  },
  methods: {
    openURL
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
}

.q-list-header {
  margin-bottom: 20px;
}
.bg-grey-2 {
  color: rgba(223, 213, 213, 0.932);
  background: #2A3A5B !important;
}
.q-item:hover {
  background-color: #E9E9E9;
}
.margin-user {
  margin-top: 10px;
}
.floating-name {
  margin-bottom: 15px;
}

</style>

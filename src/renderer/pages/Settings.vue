<template >
<div>

    <AppConfig />

    <el-divider />

    <el-row>
      <el-col :span="8">
        <div>
          <img :src="$root.getImage('assets/rpsV2.svg')" style="width: 230px; margin-bottom: 20px;" />
        </div>
        <img :src="$root.getImage('assets/buymeashisha.svg')" class="cursor-pointer" @click="$root.open(links.kofi)" />
        <img :src="$root.getImage('assets/platform.svg')" class="cursor-pointer" @click="$root.open(links.github_releases)" />
        <br>
        <img :src="'https://img.shields.io/github/commits-since/gkiokan/ps4-remote-pkg-sender/v'+$root.versions.app" class="cursor-pointer" @click="$root.open(links.github_releases_latest)" />
        <img src="https://img.shields.io/github/last-commit/gkiokan/ps4-remote-pkg-sender" class="cursor-pointer" @click="$root.open(links.github_repo)" />
        <br>
        <br>
        <img :src="$root.getImage('assets/kofi.svg')" class="cursor-pointer" @click="$root.open(links.kofi)" />

      </el-col>
      <el-col :span="16">
          Obrigado por usar meu <b>Remote Package Sender v2</b>! <br>
          <br>
          Espero que você esteja gostando do que eu já construí, e eu realmente agradeceria qualquer feedback e apoio. <br>
          <br>
          Se você quiser me apoiar e apoiar meu desenvolvimento, pode fazer isso no Ko-Fi. <br>
          Esse desenvolvimento de aplicativo de alta qualidade é feito no meu tempo livre e exige muito esforço. <br>
          Há tantos recursos legais na minha lista de tarefas que ainda não foram feitos. <br>
          Eu gostaria de investir mais tempo nessa ferramenta e te oferecer uma das melhores ferramentas de todas. <br>

      </el-col>
    </el-row>


    <el-divider />

    <el-button size="mini" @click="$store.dispatch('app/resetConfig')"> Redefinir Configurações do Aplicativo </el-button>

    <div v-if="app.config.showConfigObject">
        <div style="height: 40px" />
        <el-divider content-position="left">Seu objeto de configurações atual</el-divider>
        <div style="white-space: pre" v-html="$helper.prettyPrint(app)" />
    </div>

</div>
</template>

<script>
import { get } from 'vuex-pathify'
import links from '@/../config/links'

export default {
  name: "Settings",

  data(){ return {
      links,
  }},

  computed: {
      app: get('app'),
  },

  methods: {

  }
}
</script>

<style lang="scss" scoped>
</style>

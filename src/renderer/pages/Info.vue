<template>
<div>
    <i class="fab fa-playstation" style="font-size: 40px; margin-bottom: 10px;" v-if="false" />

    <div style="background: #222; padding: 10px 20px; text-align: center; margin-bottom: 20px;">
      <img :src="$root.getImage('assets/rpsV2.png')" style="width: 180px; margin: 0 auto;" />
    </div>

    <h2>Informações</h2>
    app: {{ $root.versions.app }} <br>
    electron: {{ $root.versions.electron }} <br>
    electron-webpack: {{ $root.versions.electronWebpack }} <br>
    plataforma: {{ platform }} <br>
    <br>
    tempo de execução: {{ time }} segundos desde o início <br>
    iniciado: {{ started }} vezes <br>
    <br>
    <el-divider />

    Obrigado a todos que fazem essa comunidade ser incrível. <br>

    <el-divider />

    Obrigado a flatz, Specter, xvortex, <br>
    e a todos os desenvolvedores iniciais pelo esforço na cena.<br>
     <br>
    Obrigado aos meus principais testadores <br>
    CyB1K, mtnjustme, Masamune3210, hypermist. <br>
    <br>
    Obrigado a Sam Daniel pelo <br>primeiro tutorial completo no windows e mac. <br>
    <br>
    Obrigado à comunidade incrível e a todos que me deram feedback que não mencionei aqui.
    Se você gostou do que eu fiz e quiser me apoiar e apoiar meu desenvolvimento, não esqueça de conferir meu ko-fi.
    <br>
    <el-divider />

    Um super obrigado à minha esposa e à sua paciência (ou falta dela) durante
    horas intermináveis de desenvolvimento e depuração de problemas.

    <el-divider />

    <el-button size="mini" @click="crash" v-if="false"> Travar! </el-button>

    <template v-if="debug">
      <pre>{{ ps4 }}</pre>
      <pre>{{ server }}</pre>
    </template>
</div>
</template>

<script>
import { get } from 'vuex-pathify'
import os from 'os'

export default {
  name: 'Info',

  data(){ return {
      debug: false,
  }},

  computed: {
      started: get('app/started'),
      time: get('app/time'),
      server: get('app/server'),
      ps4: get('app/ps4'),
      platform(){
        return os.type() + ' ' + os.release() + ' ' + os.platform()
      },
  },

  methods: {
      crash(){
          alert("Crashing" + { crash: true })
          process.crash()
      },
  }

}
</script>

<style lang="css" scoped>
</style>

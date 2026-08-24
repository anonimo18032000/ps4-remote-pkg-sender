<template>
<div>
    <div class="mb-md">
        {{ serverFiles.length }} arquivos encontrados <br>
        {{ servingFiles.length }} arquivos sendo servidos <br>
    </div>

    Servidor está <el-tag size="mini" :type="$helper.getServerStatusType(running)" >{{ running }}</el-tag> em {{ ip }}:{{ port }}<br>
    
    <br>
    <el-button size="mini" @click="$emit('hearthbeat')"> verificar hearthbeat </el-button> {{ hb }} <br>
    <br>
    <el-button size="mini" @click="startServer">Iniciar Servidor </el-button>
    <el-button size="mini" @click="$emit('stopServer')"> Parar Servidor </el-button>
    <el-button size="mini" @click="$emit('restartServer')"> Reiniciar Servidor </el-button>

</div>
</template>

<script>
import { get } from 'vuex-pathify'

export default {
  name: 'Debug',

  computed: {
      ip: get('app/server.ip'),
      port: get('app/server.port'),
      serverFiles: get('server/serverFiles'),
      servingFiles: get('server/servingFiles'),
      running: get('server/status'),
      hb(){
          return 'http://' + this.ip + ':' + this.port + '/hb'
      }
  },

  methods: {
      startServer(){
          if(this.ip.length == 0 || this.port.length == 0){
              let error = "O servidor não pode iniciar. Configure o IP e a Porta"
              this.$store.dispatch('server/addLog', error)
              this.$message({ type: 'warning', message: error });
              return
          }

          this.$emit('startServer')
      },
  }
}
</script>

<style lang="css" scoped>
</style>

<template>
<div id='server_config'>

  <el-divider content-position="left">Configuração do Servidor Local</el-divider>

  <div class="q-pl-md">
  <el-form :inline="true" label-width="150px" size="mini" label-position="left" @submit.native.prevent>
      <el-row :gutter="10">
          <el-col :span="10">
              <el-form-item label="IP do Servidor">
                  <el-select v-model="server.ip" placeholder="Interface de Rede" default-first-option>
                      <el-option :label="i.title" :value="i.ip" v-for="i in ifaces" :key="i.ip"></el-option>
                  </el-select>
              </el-form-item>
          </el-col>
          <el-col :span="10">
              <el-form-item label="Porta">
                <el-input v-model="server.port"></el-input>
              </el-form-item>
          </el-col>
          <el-col :span="4">
              <el-button size="mini" icon="fa fa-server" style="width: calc(100% - 40px)" @click="$root.openServer()"> Servidor </el-button>
          </el-col>
      </el-row>

      <el-row :gutter="10">
          <el-col :span="10">
              <el-form-item label="Aplicativo do Servidor">
                  <el-select v-model="server.app" placeholder="Aplicativo" default-first-option>
                      <el-option :label="i.title" :value="i.app" :disabled="i.disabled" v-for="i in apps" :key="i.app"></el-option>
                  </el-select>
              </el-form-item>
          </el-col>
          <el-col :span="10">
              <el-form-item label="Status">
                  <el-tag size="small" style="width:100%;" :type="$helper.getServerStatusType(status)">{{ status }}</el-tag>
              </el-form-item>
          </el-col>
          <el-col :span="4">
              <el-button size="mini" icon="el-icon-refresh" @click="$root.sendServer('refresh')"></el-button>
              <el-button size="mini" icon="el-icon-switch-button" @click="$root.sendServer('toggle')"></el-button>
          </el-col>
      </el-row>


      <el-divider content-position="right">Localização</el-divider>
      <el-row>
          <el-col :span="24">
              <el-form-item label="Caminho Base dos PKGs" class="base_path">
                <el-input placeholder="Selecione o caminho base dos seus PKGs" v-model="server.base_path" disabled>
                    <el-button slot="append" icon="el-icon-edit" @click.native="enterManuallyBasePath"> Digitar manualmente</el-button>
                    <el-button slot="append" icon="el-icon-folder" @click.native="selectBasePath"> Clique aqui para escolher o caminho</el-button>
                </el-input>
              </el-form-item>
          </el-col>
      </el-row>

      <div>
          <el-form-item label="Varredura automática">
              <el-checkbox v-model="server.auto_scan_on_startup" disabled>Varrer automaticamente o caminho base ao iniciar</el-checkbox>
          </el-form-item>
      </div>
      <div>
          <el-form-item label="Varredura profunda">
              <el-checkbox v-model="server.scan_subdir">Varrer subpastas em busca de arquivos pkg</el-checkbox>
          </el-form-item>
      </div>

      <el-divider content-position="right">Recursos</el-divider>
      <div>
          <el-form-item label="Prefixo de URL">
              <el-checkbox v-model="server.prependFullPath"> Prefixar a URL do arquivo servido com o caminho completo, tornando cada arquivo único</el-checkbox>
          </el-form-item>
      </div>
      <div>
          <el-form-item label="Verificador de Fila">
              <el-checkbox v-model="server.enableQueueScanner"> Ativa o Verificador de Fila para iniciar automaticamente a próxima instalação da Fila</el-checkbox>
          </el-form-item>
      </div>
      <div>
          <el-form-item label="Ler Cabeçalho SFO">
              <el-checkbox v-model="server.readSFOHeader" :disabled="ps4.app == 'goldhen'"> Lê o cabeçalho SFO de cada PKG e mostra as informações do PKG </el-checkbox>
          </el-form-item>
      </div>      

  </el-form>
  </div>

  <template v-if="debug">
    <pre>Server {{ server }}</pre>
    <pre>Interfaces {{ ifaces }}</pre>
  </template>

</div>
</template>

<script>
import { get, sync } from 'vuex-pathify'
import { throttle } from 'lodash'
import { remote, ipcRenderer } from 'electron'

export default {
    name: 'ServerConfig',

    data(){ return {
        debug: false,

        ifaces: [],
        apps: [
          { title: "express", app: "express", disabled: false },
          { title: "apache", app: "apache", disabled: true },
          { title: "nginx", app: "nginx", disabled: true },
          { title: "proxy", app: "proxy", disabled: true },
          { title: "remote", app: "remote", disabled: true },
          { title: "custom", app: "custom", disabled: true },
        ]
    }},

    mounted(){
        this.loadNetworkInterfaces()
    },

    computed: {
        ps4: sync('app/ps4'),
        server: get('app/server'),
        status: get('server/status'),
    },

    watch: {
        // server: {
        //     deep: true,
        //     handler: throttle(this.save(), 2000)
        // },
        'server.ip'(){ this.save() },
        'server.port'(){ this.save() },
        'server.app'(){ this.save() },
        'server.auto_scan_on_startup'(){ this.save() },
        'server.base_path'(){
            this.save()
            this.loadFiles()
        },
        async 'server.scan_subdir'(){
            this.save()
            this.loadFiles()
        },
        async 'server.prependFullPath'(){
            this.save()
            this.loadFiles()
        },
        'server.enableQueueScanner'(){
            this.save()
        },
        'server.readSFOHeader'(){
            this.save()
            this.loadFiles()
        }
    },

    methods: {
        loadNetworkInterfaces(){
            this.ifaces = this.$helper.getNetWorkInterfaces()

            if(this.ifaces.length){
                // this.server.iface = this.ifaces[0]
            }
        },

        async selectBasePath(){
            let path = await remote.dialog.showOpenDialog({ properties: ['openDirectory'] })

            if( path && !path.canceled )
                this.server.base_path = path.filePaths[0]
        },

        loadFiles(){
            this.$store.dispatch('server/loadFiles', this.server.base_path)
            this.$message({
                type: 'success',
                message: 'Arquivos recarregados'
            });
        },

        async save(){
            console.log("Saving Local Server Configuration")
            await this.$store.dispatch('app/setServer', this.server)
        },

        enterManuallyBasePath(){
            this.$prompt('Informe o caminho base', 'Caminho Base do Servidor', {
                confirmButtonText: 'OK',
                cancelButtonText: 'Cancelar',
                // inputPattern: /[\w!#$%&'*+/=?^_`{|}~-]+(?:\.[\w!#$%&'*+/=?^_`{|}~-]+)*@(?:[\w](?:[\w-]*[\w])?\.)+[\w](?:[\w-]*[\w])?/,
                // inputErrorMessage: 'Invalid Email'
            }).then(({ value }) => {
                if(value){
                    this.server.base_path = value
                    this.$message({
                        type: 'success',
                        message: 'Seu caminho base foi definido como: ' + value
                    });
                }
            }).catch(() => {
                this.$message({
                    type: 'info',
                    message: 'Entrada cancelada'
                });
            });
        }

    }
}
</script>

<style lang="scss">
.input_base_path .el-form-item__content {
  width: calc(100% - 175px);
}
</style>

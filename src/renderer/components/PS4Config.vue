<template>
<div id='server_config'>


  <el-divider content-position="left">Configuração do Playstation</el-divider>

  <div class="q-pl-md">
  <el-form :inline="true" label-width="150px" size="mini" label-position="left" @submit.native.prevent>
      <el-row :gutter="20">
          <el-col :span="10">
              <el-form-item label="IP do Playstation">
                <el-input v-model="ps4.ip"></el-input>
              </el-form-item>
          </el-col>

          <el-col :span="10">
              <el-button size="mini" icon="el-icon-search" :disabled="true">Procurar PlayStation na Rede</el-button>
          </el-col>
      </el-row>

      <el-row :gutter="20">
          <el-col :span="10">
              <el-form-item label="Aplicativo do Playstation">
                  <el-select v-model="ps4.app" placeholder="Aplicativo Alvo" default-first-option>
                      <el-option :label="app.value" :value="app.key" :disabled="app.disabled" v-for="app in ps4Apps" :key="app.key" />
                  </el-select>
              </el-form-item>
          </el-col>

          <el-col :span="9">
              <el-form-item label="Porta do Aplicativo">
                  <el-input v-model="ps4.port" :disabled="ps4.app != 'rpiOOP'" style="width: 150px"></el-input>
              </el-form-item>
          </el-col>

          <el-col :span="5">
              <el-button size="small" @click="checkPS4" style="width: 100%"> <i class="el-icon-loading" v-if="testingConnection" />  Testar conexão</el-button>
          </el-col>
      </el-row>


      <el-divider content-position="right">Parâmetros</el-divider>
      <el-row :gutter="20">
          <el-col :span="10">
              <el-form-item label="Tempo Limite da Requisição" style="margin-bottom: 0px;">
                  <el-slider v-model="ps4.timeout" :format-tooltip="(val) => val + 'ms'"
                            :step="100" :min="2000" :max="8000" style="width:160px; display: inline-block"></el-slider> <br>
              </el-form-item>
          </el-col>

          <el-col :span="10">
              <el-form-item label="Intervalo de Atualização" style="margin-bottom: 0px;">
                  <el-slider v-model="ps4.update" :format-tooltip="(val) => val + 'ms'"
                            :step="100" :min="1000" :max="5000" style="width:160px; display: inline-block"></el-slider>
              </el-form-item>
          </el-col>
      </el-row>

      <el-row :gutter="20">
          <el-col :span="10">
              <p style="font-style: italic; font-size: 13px; color: #888">
                Defina um Tempo Limite de Requisição maior se você tiver erros de timeout e tiver certeza que o resto está configurado corretamente.
              </p>
          </el-col>

          <el-col :span="12">
              <p style="font-style: italic; font-size: 13px; color: #888">
                O Intervalo de Atualização afeta a frequência de atualização do progresso de uma tarefa. Um valor maior significa mais atraso entre atualizações. <br>
              </p>
          </el-col>
      </el-row>

  </el-form>
  </div>

</div>
</template>

<script>
import { get, sync } from 'vuex-pathify'

export default {
    name: 'PS4Config',

    data(){ return {
        testingConnection: false,
        ps4Apps: [
            { value: 'PS4 RPI (flatZ)', key: 'rpi', disabled: false },
            { value: 'PS4 RPI (OOP)', key: 'rpiOOP', disabled: false },
            { value: 'PS4 GoldHEN', key: 'goldhen', disabled: false },
            { value: 'PS5 etaHEN', key: 'etaHEN', disabled: false },
            { value: 'PS4 IPI', key: 'ipi', disabled: true },
            { value: 'PS4 HB-Store', key: 'hbstore', disabled: true },
        ]
    }},

    computed: {
        ps4: sync('app/ps4'),
        server: sync('app/server'),
    },

    watch: {
        'ps4.ip'(){ this.save() },
        'ps4.app'(val){ 
            if(val == 'rpi')
                this.ps4.port = this.ps4.port_rpi

            if(val == 'rpiOOP')
                this.ps4.port = this.ps4.port_rpiOOP

            if(val == 'etaHEN'){
                this.ps4.port = this.ps4.port_etaHEN ?? 9090
                this.server.enableQueueScanner = false
            }

            if(val == 'goldhen'){
                this.ps4.port = this.ps4.port_goldhen ?? 9090
                this.server.enableQueueScanner = false     
                this.server.readSFOHeader = true           
            }

            this.save()
        },
        'ps4.port'(){ 
            if(this.ps4.app == 'rpiOOP')
              this.ps4.port_rpiOOP = this.ps4.port

            this.save()
        },
        'ps4.timeout'(){ this.save() },
        'ps4.updateInterval'(){ this.save() },
    },

    methods: {
        save(){
            console.log("Save PS4 Configuration")
            this.$store.dispatch('app/setPs4', this.ps4)
        },

        async checkPS4(){
            this.testingConnection = true

            if( this.$store.getters['app/isPS5'] )
                return await this.$ps5.checkPS5()
                    .then( () => {
                        this.testingConnection = false
                        this.$root.log("PS5 está acessível", null)
                        this.$message({ message: "PS5 está acessível", type: 'success' })
                    })
                    .catch( e => {
                        this.testingConnection = false
                        console.log(e)
                        this.$root.log("Verificar Playstation: PS5 não está acessível", e)
                        this.$message({ message: "PS5 não está acessível", type: 'error' })
                    })

            // GoldHEN's embedded RPI speaks a raw TCP protocol, not the flatZ
            // RPI HTTP API that $ps4.checkPS4() targets (GET /api/is_exists).
            // Hitting that HTTP endpoint against GoldHEN's TCP listener always
            // fails, even when GoldHEN is reachable and working fine - so this
            // needs its own check using the same raw TCP connect GoldHEN's
            // install request uses.
            if( this.ps4.app == 'goldhen' )
                return await this.$ps4_goldhen.checkPS4()
                    .then( () => {
                        this.testingConnection = false
                        this.$root.log("PS4 (GoldHEN) está acessível", null)
                        this.$message({ message: "PS4 (GoldHEN) está acessível", type: 'success' })
                    })
                    .catch( e => {
                        this.testingConnection = false
                        console.log(e)
                        this.$root.log("Verificar Playstation: PS4 (GoldHEN) não está acessível", e)
                        this.$message({ message: "PS4 (GoldHEN) não está acessível", type: 'error' })
                    })

            this.$ps4.checkPS4()
                .then( (res) => {
                    this.testingConnection = false
                    this.$root.log("PS4 está acessível", { status: res.status, statusText: res.statusText })
                    this.$message({ message: "Verificação do Playstation. PS4 está acessível", type: 'success' })
                })
                .catch( e => {
                    this.testingConnection = false
                    this.$root.log("Verificar Playstation: PS4 não está acessível", e)
                    this.$message({ message: "PS4 não está acessível.", type: 'error' })
                })
        },


    }
}
</script>

<style lang="css" scoped>
</style>

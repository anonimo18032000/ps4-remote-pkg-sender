<template>
<div id='server_config'>

  <el-divider content-position="left">Configurações do Aplicativo</el-divider>
  <div class="q-pl-md">
  <el-form :inline="true" label-width="150px" size="mini" label-position="left" @submit.native.prevent>
      <el-row>
        <el-col :span="8">
            <el-form-item label="Idioma">
              <el-select v-model="config.lang" placeholder="Idioma" default-first-option>
                  <el-option :label="lang.value" :value="lang.key" :disabled="lang.disabled" v-for="lang in languages" :key="lang.key" />
              </el-select>
            </el-form-item>
        </el-col>
        <el-col :span="16">
            <p style="font-style: italic; font-size: 13px; color: #888; padding-top: 5px">
              *Apenas em preparação. Se alguém quiser contribuir, basta abrir uma nova Issue com [feature/language].
            </p>
        </el-col>
      </el-row>

      <el-row>
        <el-col :span="8">
            <el-form-item label="Estilo">
              <el-select v-model="config.style" placeholder="Estilo" default-first-option>
                  <el-option label="Modo Claro" value="light" />
                  <el-option label="Modo Escuro" value="dark" />
                  <el-option label="Preto Puro" value="pureblack" />
              </el-select>
            </el-form-item>
        </el-col>
        <el-col :span="16">
            <p style="font-style: italic; font-size: 13px; color: #888; padding-top: 5px">
              Quer um esquema de cores específico? Crie uma nova Issue com [feature/style]
            </p>
        </el-col>
      </el-row>

      <el-row>
        <el-col :span="8">
            <el-form-item label="Estilo">
              <el-select v-model="config.titleBar" placeholder="Barra de Título" default-first-option>
                  <el-option label="Padrão do Sistema" value="default" />
                  <el-option label="Mac" value="mac" />
                  <el-option label="Mac chromatic" value="mac-chromatic" />
                  <el-option label="Windows / Linux" value="win" />
                  <el-option label="Nenhum" value="none" />
              </el-select>
            </el-form-item>
        </el-col>
        <el-col :span="16">
            <p style="font-style: italic; font-size: 13px; color: #888; padding-top: 5px">
              Define a aparência da Barra de Título
            </p>
        </el-col>
      </el-row>
  </el-form>
  </div>


  <!--
    ***************************
    Features
    ***************************
  -->
  <el-divider content-position="left">Lista de Recursos</el-divider>
  <div class="q-pl-md">
  <el-form :inline="true" label-width="150px" size="mini" label-position="left" @submit.native.prevent>
      <el-row>
        <el-col :span="8">
            <el-form-item label="Notificações">
                <el-checkbox v-model="config.enableSystemNotifications"> Ativar Notificações do Sistema </el-checkbox>
            </el-form-item>
        </el-col>
        <el-col :span="16">
            <p style="font-style: italic; font-size: 13px; color: #888; padding-top: 5px">
              Envia Notificações do Sistema quando uma instalação começa e termina
            </p>
        </el-col>
      </el-row>

      <el-row>
        <el-col :span="8">
            <el-form-item label="Links Externos">
                <el-checkbox v-model="config.enableExternalLinks"> Ativar adição de Links externos </el-checkbox>
            </el-form-item>
        </el-col>
        <el-col :span="16">
            <p style="font-style: italic; font-size: 13px; color: #888; padding-top: 5px">
              Adicione PKGs à sua Central de Processamento a partir de uma URL externa (experimental)
            </p>
        </el-col>
      </el-row>

      <el-row>
          <el-col :span="8">
              <el-form-item label="HB-Store">
                  <el-checkbox v-model="config.useHB"> Ativar aba HB-Store</el-checkbox>
              </el-form-item>
          </el-col>
          <el-col :span="16">
              <p style="font-style: italic; font-size: 13px; color: #888; padding-top: 5px">
                Acesso direto à HB-Store oficial do pkg-zone.com
              </p>
          </el-col>
      </el-row>

      <el-row v-if="config.useHB">
          <el-col :span="8">
              <el-form-item label="Modo do HB-Store">
                  <el-select v-model="config.useHBMode" placeholder="Modo" default-first-option>
                      <el-option :label="mode.value" :value="mode.key" :disabled="mode.disabled" v-for="mode in HBModes" :key="mode.key" />
                  </el-select>
              </el-form-item>
          </el-col>

          <el-col :span="16">
              <p style="font-style: italic; font-size: 13px; color: #888; padding-top: 5px" v-if="config.useHBMode == 'legacy'">
                  <b>Modo Legado</b> é para a API atual do HB-Store <br>
              </p>
              <p style="font-style: italic; font-size: 13px; color: #888; padding-top: 5px" v-if="config.useHBMode == 'refactored'">
                  <b>Modo Refatorado</b> permite conectar à nova API do HB-Store <br>
              </p>
              <p style="font-style: italic; font-size: 13px; color: #888; padding-top: 5px" v-if="config.useHBMode == 'pkg-zone'">
                  <b>PKG-Zone</b> conecta à API oficial do HB-Store do pkg-zone.com <br>
              </p>              
              <p style="font-style: italic; font-size: 13px; color: #888; padding-top: 5px" v-if="config.useHBMode == 'custom'">
                  <b>Modo Personalizado</b> permite conectar ao seu próprio Servidor CDN do HB-Store <br>
              </p>
          </el-col>
      </el-row>

      <el-row v-if="config.useHB && config.useHBMode">
          <el-col :span="8">
              <el-form-item label="CDN do HB-Store" class="full-width full-width-150">
                  <el-input v-model="config.useHBRoot" style="width: 100%;" v-if="config.useHBMode != 'custom'" :disabled="config.useHBMode == 'pkg-zone'"> </el-input>
                  <el-input v-model="config.useHBCustomRoot" style="width: 100%;" v-if="config.useHBMode == 'custom'"> </el-input>
              </el-form-item>
          </el-col>

          <el-col :span="16">
              <p style="font-style: italic; font-size: 13px; color: #888; padding-top: 5px; padding-left: 30px;">
                  Deve terminar com barra (ex.: dominio.com<b>/</b>)
              </p>
          </el-col>
      </el-row>

      <div style="height: 30px" />

      <div>
          <el-form-item label="Mostrar Objeto de Configuração" label-width="300px">
              <el-checkbox v-model="config.showConfigObject"> Mostrar meu Objeto de Configurações completo </el-checkbox>
          </el-form-item>
      </div>

  </el-form>
  </div>

  <template v-if="debug">
      <pre>Config {{ config }}</pre>
  </template>

</div>
</template>

<script>
import { get, sync } from 'vuex-pathify'

export default {
    name: 'AppConfig',

    data(){ return {
        debug: false,

        languages: [
            { key: 'en', value: 'English', disabled: false },
            { key: 'de', value: 'German', disabled: true },
            { key: 'fr', value: 'French', disabled: true },
            { key: 'sp', value: 'Spain', disabled: true },
            { key: 'tr', value: 'Turkish', disabled: true },
            { key: 'gr', value: 'Greek', disabled: true },
        ],

        HBModes: [
            // { key: 'legacy', value: 'Legacy', disabled: true},  // #deprecated        
            { key: 'refactored', value: 'Refactored', disabled: false },
            { key: 'pkg-zone', value: 'PKG-Zone', disabled: false },
            { key: 'custom', value: 'Custom CDN', disabled: false },
        ]
    }},

    mounted(){

    },

    computed: {
        config: get('app/config'),
    },

    watch: {
        'config.lang'(){ this.save() },
        'config.style'(){ this.save() },
        'config.titleBar'(){ this.save() },
        'config.useHB'(){ this.save() },
        'config.useHBMode'(){ 
            if( this.config.useHBMode == 'pkg-zone' )
                this.config.useHBRoot = 'http://api.pkg-zone.com/'

            this.save() 
        },
        'config.useHBRoot'(){ this.save() },
        'config.useHBCustomRoot'(){ this.save() },
        'config.showConfigObject'(){ this.save() },
        'config.enableExternalLinks'(){ this.save() },
        'config.enableSystemNotifications'(){ this.save() },
    },

    methods: {
        save(){
            console.log("Saving App Configuration")
            this.$store.dispatch('app/setConfig', this.config)
        },

    }
}
</script>

<style lang="scss">

</style>

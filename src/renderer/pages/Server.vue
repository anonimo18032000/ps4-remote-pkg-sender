<template>
<div class="ServerView">

    <el-row style="margin-bottom: 20px">
        <el-button :type="$helper.is(tab == 'server', 'success active', '')" data-umami-event="tab.server" @click="$root.serverTab = 'server'"> Arquivos Base </el-button>
        <el-button :type="$helper.is(tab == 'dragged', 'success active', '')" data-umami-event="tab.dragged"  @click="$root.serverTab = 'dragged'"> Arquivos Arrastados </el-button>
        <el-button disabled> Recurso futuro: Arquivos de Hosts! </el-button>
    </el-row>

    <el-row style="margin-bottom: 20px;">
      <el-col :span="20" style="display: flex">
            <el-button @click="reload" size="small" icon="el-icon-refresh-left" style="margin-right: 10px; height: 32px;" v-if="tab == 'server'"> Recarregar </el-button>

            <el-form class="base_path_input_form" v-if="$root.serverTab == 'server'">
            <el-form-item style="margin: 0px; width: 100%;">
                <el-input size="small" placeholder="Selecione o caminho base dos seus PKGs" v-model="server.base_path" disabled>
                    <el-button size="mini" slot="append" icon="el-icon-edit" @click.native="enterManuallyBasePath"> </el-button>
                    <el-button size="mini" slot="append" icon="el-icon-folder" @click.native="selectBasePath"> </el-button>
                    <el-button size="mini" slot="append" icon="el-icon-plus" @click.native="addAllFilesToQueue"> Adicionar todos à Fila </el-button>
                </el-input>
            </el-form-item>            
            </el-form>

            <el-button size="small" icon="el-icon-delete" @click.native="removeFilesFromDragged" v-if="tab == 'dragged'"> Remover todos os arquivos </el-button>
            <el-button size="small" icon="el-icon-plus" @click.native="addAllFilesToQueue" v-if="tab == 'dragged'"> Adicionar todos à Fila </el-button>
      </el-col>
      <el-col :span="4">
            <el-input v-model="search" size="small" placeholder="Pesquisar" prefix-icon="fas fa-search" />
      </el-col>
    </el-row>


    <el-table :data="files" v-loading="loading" class="file"
        element-loading-text="Carregando arquivos do servidor"
        element-loading-spinner="el-icon-loading"
        element-loading-background="rgba(255, 255, 255, 0.8)"
        style="width: 100%">
        <el-table-column type="expand">
          <template slot-scope="scope">
              <el-tag size="small" type="info" style="margin-bottom: 3px;"> Caminho: {{ scope.row.path }} </el-tag> <br>
              <el-tag size="small" type="info" style="margin-bottom: 3px;"> URL do PKG: {{ scope.row.url }} </el-tag> <br>
              <el-tag size="small" type="info" style="margin-bottom: 3px;"> URL do Icon0: {{ scope.row.image }} </el-tag> <br>
              <el-tag size="small" type="info" style="white-space:pre; height: auto; line-height: 1.1;" v-if="debugItemInRow">{{ scope.row }}</el-tag>
          </template>
        </el-table-column>

        <el-table-column label="Capa" width="100" v-if="sfoEnabled">
            <template slot-scope="scope">
                <div class='image' :style="{ backgroundImage: 'url('+scope.row.image+')' }" />
            </template>
        </el-table-column>        

        <el-table-column prop="name" label="Nome">
            <template slot-scope="scope">
                {{ scope.row.name }} <small v-if="scope.row.sfo?.readSFOHeader">(v{{ scope.row.sfo.APP_VER}})</small>
                <el-tag size="small" :type="$helper.getAppStoreType(scope.row.sfo.CATEGORY)" style="margin-left: 10px; margin-bottom: 3px;" v-if="scope.row.sfo?.readSFOHeader">{{ scope.row.sfo.CATEGORY }}</el-tag>
                
                <div v-if="scope.row.sfo?.readSFOHeader">                
                    <el-tag size="small" type="info"> {{ scope.row.sfo.CONTENT_ID}} </el-tag>
                </div>
            </template>
        </el-table-column>

        <el-table-column label="Ext" width="100" v-if="showExtension">
            <template slot-scope="scope">
                <el-tag size="mini"
                      :type="scope.row.ext === '.pkg' ? 'primary' : 'success'"
                      disable-transitions>{{scope.row.ext}}</el-tag>
            </template>
        </el-table-column>

        <el-table-column prop="cusa" label="ID do Título" width="110" align="center" v-if="showCUSA">
            <template slot-scope="scope">
                <small style="font-size:12px">{{ scope.row.cusa }}</small>
            </template>
        </el-table-column>

        <el-table-column prop="status" label="Status" width="120" align="center">
          <template slot-scope="scope">
              <el-tag size="small" plain :type="$helper.getFileStatus(scope.row.status)">{{ scope.row.status }}</el-tag>
          </template>
        </el-table-column>

        <el-table-column prop="size" label="Tamanho" width="120" align="right">
          <template slot-scope="scope">
              <el-tag size="small" plain :type="$helper.getFileSizeType(scope.row.size)">{{ scope.row.size }}</el-tag>
          </template>
        </el-table-column>

        <el-table-column label="Progresso" width="100px" v-if="showPercentage">
            <template slot-scope="scope">
                <el-tag size="mini" v-if="0">n/a</el-tag>
                <el-progress :stroke-width="15" :percentage="scope.row.percentage" :text-inside="true" stroke-linecap="square"></el-progress>
            </template>
        </el-table-column>

        <el-table-column label="Ações" width="100" align="right">
            <template slot-scope="scope">
                <el-button circle size="small" icon="fa fa-minus" @click="removeFromQueue(scope.row)" v-if="scope.row.status == 'in queue'" />
                <el-button circle size="small" icon="el-icon-plus" @click="addToQueue(scope.row)" v-if="scope.row.status != 'in queue'" />
                <el-button circle size="small" icon="fa fa-cloud-download-alt" @click="check(scope.row.url)" v-if="tab == 'server'" />
                <el-button circle size="small" icon="el-icon-delete" @click="removeFileFromDragged(scope.row)" v-if="tab == 'dragged'" />
            </template>
        </el-table-column>
    </el-table>

    <template class='file_list' v-if="debug">
        <pre>{{ server.base_path }}</pre>
        <pre>{{ files }}</pre>
    </template>

</div>
</template>

<script>
import fs from 'fs'
import path from 'path'
import { get, sync } from 'vuex-pathify'
import { remote, ipcRenderer } from 'electron'

import express from 'express'
import http from 'http'

export default {
    name: 'ServerList',

    data(){ return {
        // files: [],
        debug: false,
        debugItemInRow: true,
        
        showExtension: false,
        showCUSA: true,
        showVersion: false,
        showPercentage: false,

        search: '',

        app: null,
        http: null,
    }},

    mounted(){
        // this.run()
        this.search = ''
    },

    computed: {
        server: sync('app/server'),
        draggedFiles: get('server/draggedFiles'),
        draggedServingFiles: get('server/draggedServingFiles'),
        serverFiles: get('server/serverFiles'),        
        servingFiles: get('server/servingFiles'),
        queueFiles: get('queue/queue'),
        routes: get('server/routes'),
        loading: get('server/loading'),
        sfoEnabled: get('app/getReadSFOHeader'),
        files(){ 
            let search = this.search.toLowerCase()
            let finalFiles = this.servingFiles

            if( this.tab == 'dragged' )
                finalFiles = this.draggedServingFiles

            if(search.length != 0)
              return finalFiles.filter( file =>
                  file.name.toLowerCase().includes(search) || 
                  file.cusa.toLowerCase().includes(search) ||
                  file.status.toLowerCase().includes(search)
                )

            // legacy
            return finalFiles
        },
        tab(){
            return this.$root.serverTab
        },
    },

    methods: {
        reload(){
            if(!this.server.base_path){
                this.$message({
                  type: 'warning',
                  message: 'Nenhum caminho base do servidor definido. Configure primeiro.'
                });
                return
            }

            console.log("Reload files at base path. Triggered though Server-List")
            // this.$store.dispatch('server/startLoading')
            // this.$store.dispatch('server/loadFiles', this.server.base_path)
            this.loadFiles()

            // this.$store.dispatch('server/stopLoading')
            // setTimeout( () => this.$store.dispatch('server/stopLoading'), 2000)
            // console.log(this.routes)
        },

        check(url){
            this.$root.openWithAutoclose(url)
        },

        run(){
            this.$store.dispatch('server/resetLogs')

            setInterval( () => {
                let x = this.servingFiles[0]
                console.log("run test", x)
                x.percentage++

                this.$store.dispatch('server/addLog', 'just a test')
            }, 1000)
        },

        addToQueue(file){
            let find = this.$store.getters['queue/isInQueueUnique'](file)

            if(!find){
                file.status = 'in queue'
                this.$store.dispatch('queue/addToQueue', file)
                this.$root.track({ name: 'addToQueue', data: { name: 'Added to Queue', value: file.name } })
            }
            else {
                if(file.status == 'serving')
                  file.status = 'in queue'

                this.$message({
                    message: file.name + ' já está na Fila',
                    type: 'warning'
                })
            }
        },

        removeFromQueue(file, notify=true){
            let servingFile = this.$store.getters['server/findFile'](file)

            if(servingFile && servingFile.status == 'in queue'){
                servingFile.status = 'serving'
                this.$store.dispatch('queue/removeFromQueue', file)
                this.$root.track({ name: 'removeFromQueue', data: { name: 'Removed from Queue', value: file.name } })
            }
            else {
                if( notify )
                    this.$message({
                        message: "Não é possível remover " + file.name + " da fila porque ela está em outro estado",
                        type: 'warning'
                    })
            }
        },

        addAllFilesToQueue(){
            this.files.map( file => {
                if( !this.$store.getters['queue/isInQueue'](file) )
                    this.addToQueue(file)
            })

            this.$message({
              type: 'success',
              message: 'Todos os arquivos foram adicionados à Fila'
            });        
            this.$root.track({ name: 'addAllFilesToQueue', data: { name: 'Add all files to the Queue' } })    
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
                      message: 'Seu caminho base foi definido como:' + value
                    });
                    this.loadFiles()
                }
            }).catch(() => {
                this.$message({
                  type: 'info',
                  message: 'Entrada cancelada'
                });
            });
        },

        async selectBasePath(){
            let path = await remote.dialog.showOpenDialog({ properties: ['openDirectory'] })

            if( path && !path.canceled ){
                // console.log("Path changed in Server Tab.")
                this.server.base_path = path.filePaths[0]
                this.$store.dispatch('app/setServer', this.server)
                this.loadFiles()
            }
        },

        loadFiles(){
            this.$store.dispatch('server/loadFiles', this.server.base_path)
            
            this.$message({
                type: 'success',
              message: 'Arquivos recarregados'
            });
            this.$root.track({ name: 'reload', data: { name: 'Reload Server files from base Path' } })
        },

        removeFilesFromDragged(){
            let leftFilesWithNoQueue = this.draggedServingFiles.filter( file => file.status != 'serving')
            this.$store.dispatch('server/setDraggedFiles', leftFilesWithNoQueue)

            this.$message({
                type: 'success',
              message: 'Arquivos que não estavam sendo servidos foram removidos'
            });            
            this.$root.track({ name: 'removeFilesFromDragged', data: { name: 'Remove all dragged Items' } })
        },

        removeFileFromDragged(file){
            let fileInQueue = this.$store.getters['queue/isInQueue'](file)

            if( fileInQueue ){
                const h = this.$createElement
                return this.$msgbox({
                    title: "Remover Arquivo da Lista",
                    message: h('div', null, [
                        h('span', null, " "),
                        h('br', null),
                        h('b', null, file.name),
                        h('br', null),
                        h('span', null, 'está na Fila'),
                        h('br', null),
                        h('span', null, 'Tem certeza que deseja remover o arquivo?')
                    ]),
                    showCancelButton: true,
                })
                .then( _ => {
                    this.removeFileFromDraggedHandler(file)
                })
                .catch( _ => {})                                
            }

            this.removeFileFromDraggedHandler(file)
        },

        removeFileFromDraggedHandler(file){
            this.removeFromQueue(file, false)
            let cleaned = this.draggedServingFiles.filter( f => f.path != file.path )
            this.$store.dispatch('server/setDraggedFiles', cleaned)
            this.$root.track({ name: 'removeFileFromDraggedHandler', data: { name: 'Remove dragged File from List', value: file.name } })
        }

    }
}
</script>

<style lang="scss" scoped>
.path_input_tag {
  margin-right: 10px;
  max-width: 100%;
  overflow: hidden;
}

.base_path_input_form {
    width: 100%; 
    margin-right: 10px; 
    margin-bottom: 0px;
}

.base_path_input_form .el-form-item__content {
    line-height: 1;
}
</style>

<template>
<div style='max-width: 960px'>

  <ServerConfig />

  <div style="height: 30px" />

  <PS4Config />

  <el-divider />

  <el-button size="mini" @click="save" v-if="false">Salvar Configuração </el-button>

  <el-button size="mini" @click="reset">Redefinir Configuração</el-button>

</div>
</template>

<script>
import { get } from 'vuex-pathify'

export default {
    name: 'Config',

    computed: {
        app: get('app'),
        server: get('app/server'),
    },

    methods: {
        save(){
            console.log("Saving Local Server Configuration")
            // this.$store.dispatch('app/setServer', this.server)
            this.$store.dispatch('app/save')
        },

        reset(){
          this.$confirm('Isso vai definir todos os valores de configuração para os valores iniciais. O servidor será parado.', 'Redefinir Configuração',
                {
                  confirmButtonText: 'OK',
                  cancelButtonText: 'Cancelar',
                  type: 'warning',
                  center: true,
                })
                .then(() => {
                    console.log("Reset Local Server Configuration")
                    this.$store.dispatch('app/reset')
                    this.$root.sendServer('stop')
                    this.$message({
                      type: 'success',
                      message: 'A configuração foi redefinida'
                    });
                })
                .catch(() => {
                    // this.$message({
                    //   type: 'info',
                    //   message: 'Reset action canceled'
                    // });
                });
        }
    }
}
</script>

<template>
<div>

  <h2>Downloads</h2>

  <el-row>
    <el-col style="width: 240px;">
        <img :src="$root.getImage('assets/rpi.png')" class="icon0" />
    </el-col>
    <el-col :span="12">
        <b>Remote Package Installer</b> por flatZ <br>
        <br>
        O primeiro Remote Package Installer da cena, feito por flatZ. <br>
        Foi construído em 2019 e ainda não recebeu atualizações recentes. <br>
        É conhecido como a forma legada e padrão de instalar PKGs pela Rede. <br>
        <br>
        Qualquer problema é tratado no Guia de Solução de Problemas. <br>
        <br>
        <el-button @click="$root.open(links.ps4_rpi)"> Download </el-button>
        <el-button @click="$root.open(links.ps4_rpi_github)"> GitHub </el-button>
    </el-col>
  </el-row>

  <div style="height: 40px" />

  <el-row>
    <el-col style="width: 240px;">
        <img :src="$root.getImage('assets/rpioop.png')" class="icon0" />
    </el-col>
    <el-col :span="12">
        <b>Remote Package Installer [Open Orbis Port]</b> por njzydark <br>
        <br>
        Fork do RPI OOP feito por Njzydark, com as atualizações mais recentes. <br>
        Confira os repositórios no GitHub para comparar as atualizações. <br>
        Se o RPI do flatZ não funcionar pra você, vale testar esse aqui. <br>
        <br>
        <el-button @click="$root.open(links.ps4_rpioop_njzydark)"> Download </el-button>
        <el-button @click="$root.open(links.ps4_rpioop_github_njzydark)"> GitHub </el-button>
    </el-col>
  </el-row>

  <div style="height: 40px" />

  <el-row>
    <el-col style="width: 240px;">
        <img :src="$root.getImage('assets/rpioop.png')" class="icon0" />
    </el-col>
    <el-col :span="12">
        <b>Remote Package Installer [Open Orbis Port]</b> por backporter<br>
        <br>
        O backporter está trabalhando em uma nova versão do Remote Package Installer <br>
        portando-o da versão do flatZ para uma nova, com o Open Orbis SDK. <br>
        <br>
        Abaixo você encontra a versão direta 1.0 e o repositório no GitHub. <br>
        <br>
        <el-button @click="$root.open(links.ps4_rpioop)"> Download </el-button>
        <el-button @click="$root.open(links.ps4_rpioop_github)"> GitHub </el-button>
    </el-col>
  </el-row>

  <div style="height: 40px" />

  <el-row>
    <el-col style="width: 240px;">
        <img :src="$root.getImage('assets/hb.png')" class="icon0" />
    </el-col>
    <el-col :span="12">
        <b>HB-Store R2</b> por LightningMods <br>
        <br>
        A loja oficial de aplicativos Homebrew, construída pela Darksoftware LLC. <br>
        <br>
        Graças ao LightningMods, tenho permissão para usar a API no aplicativo. <br>
        Isso significa que você pode Baixar e Instalar Apps da HB-Store diretamente. <br>
        <br>
        <el-button @click="$root.open(links.ps4_hbstore)"> Download </el-button>
        <el-button @click="$root.open(links.pkgzone)" > Visitar PKG-Zone.com </el-button>
    </el-col>
  </el-row>

  <div style="height: 40px" />

  <el-row>
    <el-col style="width: 240px;">
        <img :src="$root.getImage('assets/hb-store-cdn-server.jpeg')" class="icon0" />
    </el-col>
    <el-col :span="12">
        <b>HB-Store CDN Server</b> por Gkiokan <br>
        <br>
        Com o CDN Server você pode hospedar todos os seus arquivos PKG locais com <br>
        facilidade para a HB-Store. Ele suporta correção de nomes e varredura de subpastas <br>
        com base em um caminho base, e também funciona em várias plataformas.<br>
        <br>
        <i style="font-style: italic;">Também existe uma Versão CLI, que inclusive é suportada oficialmente pelo <b>RetroNAS</b>!</i> <br>
        <br>
        <el-button @click="$root.open(links.hb_store_cdn_server_latest)"> Download </el-button>
        <el-button @click="$root.open(links.hb_store_cdn_server_repo)" > CDN Server </el-button>
        <el-button @click="$root.open(links.hb_store_cdn_server_cli_repo)" > Versão CLI do CDN Server </el-button>
    </el-col>
  </el-row>

  <div style="height: 40px" />

  <el-row>
    <el-col style="width: 240px;">
        &nbsp;
    </el-col>
    <el-col :span="12">
        <b>Seu App aqui?</b> <br>
        <br>
        Se você quiser adicionar seu App Homebrew à Área de Downloads <br>
        me manda mensagem no Discord em <el-tag size="mini">Gkiokan#0735</el-tag> <small style="font-size: 13px">(legado)</small> ou <el-tag size="mini">gkiokan</el-tag><br>

    </el-col>
  </el-row>

  <div style="height: 40px" />
</div>
</template>

<script>
import links from '@/../config/links'

export default {
    name: 'Download',

    data(){ return {
        links,
    }},

    methods: {

    }
}
</script>

<style lang="scss" scoped>
b {
  font-weight: bold;
}

.icon0 {
  display: block;
  width: 200px; height: 200px;
  border-radius: 4px;
}
</style>

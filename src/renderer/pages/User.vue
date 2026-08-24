<template >
<div>

    <div style="margin-bottom: 20px;">
      <img :src="$root.getImage('assets/kofi.svg')" class="cursor-pointer" @click="$root.open(links.kofi)" />
    </div>

    <h2>Recursos de Usuário e mais por vir na 2.10.x</h2>
    <el-divider content-position="left" v-if="false"> Recursos de Usuário e mais por vir na 2.10.x </el-divider>

    Olá a todos! <br>
    <br>
    Tem sido uma longa jornada até aqui, e vocês se saíram muito bem! <br>
    Com 53 mil downloads em agosto de 2023, isso é bem impressionante <br>
    <small style="font-size: 90%">(67 mil downloads em fevereiro de 2024) </small><br>
    <small style="font-size: 90%">(79 mil downloads em maio de 2024) </small><br>
    Obrigado por usar o aplicativo. Qualquer apoio é bem-vindo, e não esqueça de conferir meus canais. <br>
    <br>
    Vou falar sobre algumas mudanças grandes que virão em breve ou mais tarde, dependendo do apoio de vocês. <br>
    Essa atualização em particular (v2.9.1) foi adiada por quase um ano (última atualização em agosto de 2022). <br>
    Coloquei uma <el-tag size="mini" type="danger" class="cursor-pointer" @click="$root.open('https://ko-fi.com/gkiokan/goal?g=0')" target="_blank">Meta no Ko-Fi</el-tag>, então quem quiser doar é bem-vindo! <br>
    Quanto mais rápido a meta for atingida, mais tempo posso dedicar às atualizações - a escolha é sua :) <br>
    <br>
    Nota: Esta tela vai virar a Central de Perfil do Usuário após a atualização<br>

    <el-divider />

    <h2 style="font-size: 20px"> O que vem por aí? </h2>
    <b>0) Suporte a PS5 </b> [Concluído]<br>
    Com a 2.10.0+ somos o primeiro aplicativo que suporta instalação de PKG através de requisições de aplicativo. <br>
    A primeira prototipagem funciona bem, mas ainda podemos precisar de mais feedback pra considerar isso estável. <br>
    Obrigado ao etaHEN (@LM) e a todos os devs que trabalharam nisso - um grande passo adiante. <br>

    <div class="space-50" />

    <b>1) Números de Série do Aplicativo </b> [Concluído] <br>
    Todo aplicativo na versão 2.9.0+ agora terá seu próprio número de série. <br>
    Isso será necessário para futura criptografia e descriptografia de mensagens e dados para os recursos. <br>

    <div class="space-50" />

    <b>2) Serviço de API RPSV2 </b> [preparado] <br>
    Com o Serviço de API RPSV2 poderemos lidar com configurações dinâmicas no futuro. <br>
    Criei uma primeira api de configuração e vou testar na versão 2.9.1 o quão bem isso funciona para <br>
    todos os usuários. Isso traz muitos benefícios, como expandir Temas ou Idiomas sem precisar <br>
    de novas versões, conectar a Extensão do Chrome ou o app Mobile (mais adiante, assim que estiverem prontos e publicados). <br>
    <br>
    Isso inclui também um Gerenciamento de Usuários, para conectar todos os usuários do App entre si para diferentes propósitos. <br>
    Ou seja, você poderá se registrar e entrar na sua Conta e gerenciar várias coisas direto pelo App. <br>

    <div class="space-50" />

    <b>3) Tradução </b> [Em andamento] <br>
    Tão pedida e ainda não disponível. O motivo é que eu não estava satisfeito com uma implementação nativa <br>
    dos arquivos de tradução. Mas com base no Serviço de API RPSV2 isso será feito em breve. <br>

    <div class="space-50" />

    <b>4) Arquivos de Hosts Remotos </b> [planejado] <br>
    Isso vai ser realmente enorme. Usuários poderão compartilhar seus arquivos PKG entre si. <br>
    Esse recurso será possível graças aos Pontos 1 e 2. O Serviço de API RPSV2 vai cuidar de toda a <br>
    configuração e comunicação, enquanto os números de série vão atuar como uma criptografia de handshake <br>
    entre os usuários. Além disso, haverá um túnel seguro. Pelo menos é isso que está planejado. <br>
    <br>
    Imagine só criar seu próprio Host de PKG e compartilhar isso como um Serviço com seus amigos! <br>
    Eles não precisariam baixar e instalar arquivos, poderiam fazer isso direto pelo Serviço. <br>

    <div class="space-50" />

    <b>5) Cliente FTP Integrado </b> <br>
    Como o nome diz, o Cliente FTP Integrado vai permitir navegar diretamente no seu PS4 <br>
    e mover arquivos entre pastas. Isso pode ser interessante para o método de instalação IPI <br>
    ou como alternativa para gerenciar arquivos de/para/no PS4. <br>

    <div class="space-50" />

    <b>6) Gerenciador de Cheats do GoldHEN </b> <br>
    Não tenho certeza de quem precisa disso, mas deve ser bem simples de construir com base no Ponto 4 e estender <br>
    a funcionalidade sincronizando com o Repositório de Cheats do GoldHEN no GitHub. Legal o suficiente como bônus. <br>

    <div class="space-30" />


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

</div>
</template>

<script>
import { get } from 'vuex-pathify'
import links from '@/../config/links'

export default {
  name: "User",

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

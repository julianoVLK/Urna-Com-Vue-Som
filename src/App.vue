<template>
  <div id="app">
    <div class="urna">
      <TelaUrna
      :tela="telaAtual"
      :numeroVoto="numeroVoto"
      :quantidadeNumeros="quantidadeNumeros"
      :candidato="candidato"
      />
      <TecladoUrna 
      :adicionarNumero="adicionarNumero"
      :corrigir="corrigir"
      :confirmar="confirmar"
      :votarEmBranco="votarEmBranco"
      />
    </div>
  </div>
</template>

<script>

import '@/css/global.css';
import confirmAudio from '@/assets/audios/confirm.wav';
import keyAudio from '@/assets/audios/key.wav';

import TelaUrna from '@/components/Tela';
import TecladoUrna from '@/components/Teclado';

export default {
  name: 'App',
  components: {
    TecladoUrna,
    TelaUrna
  },
  methods: {
    adicionarNumero(numero){

      this.executarSom(keyAudio);
      
      if(this.numeroVoto.length == this.quantidadeNumeros) {
        return false;
      }

      this.numeroVoto += ''+numero;

      this.verificarCandidato();
    },

    verificarCandidato(){
      if(this.numeroVoto.length < this.quantidadeNumeros) {
        return false;
      }

      //Verifica se tem candidato no JSON
      if(this.candidatos[this.telaAtual][this.numeroVoto]){
        this.candidato = this.candidatos[this.telaAtual][this.numeroVoto];
        return true;
      }

      //Regra para voto nulo
      this.candidato = {
        nome: 'Voto Nulo',
        partido: 'Voto Nulo',
        imagem: ''
      }
    },

    corrigir(){
      this.executarSom(keyAudio);
      
      this.limpar();
    },

    limpar(){
      this.candidato = {}
      this.numeroVoto = ''
    },

    confirmar(){
      if(this.numeroVoto.length < this.quantidadeNumeros) {
        return false;
      }

      return this.avancarTela();
    },

    avancarTela(){
      this.executarSom(confirmAudio);

      if(this.telaAtual=='prefeito'){
        this.telaAtual = 'vereador';
        this.quantidadeNumeros = 5;
        return this.limpar();
      }

      this.telaAtual = 'fim';

      var instanciaAtual = this;

      setTimeout(function(){
        instanciaAtual.telaAtual = 'prefeito';
        instanciaAtual.quantidadeNumeros = 2;
        return instanciaAtual.limpar();
      },3000);
    },

    votarEmBranco(){
      if(this.telaAtual == 'fim'){
        return false;
      }

      this.limpar();
      this.avancarTela();
    },

    executarSom(arquivoSom){
      if(arquivoSom){
        let audio = new Audio(arquivoSom);
        audio.play();
      }
    }
  },
  data(){
    return {
      telaAtual: 'prefeito',
      numeroVoto: '',
      quantidadeNumeros: 2,
      candidato: {},
      candidatos: {
        "prefeito":{
          "01":{
            "nome": "Ash",
            "partido": "Pokemon",
            "imagem": "https://raw.githubusercontent.com/william-costa/wdev-urna-eletronica-resources/master/images/ash.png"
          },
          "08":{
            "nome": "Vegeta",
            "partido": "Dragon Ball",
            "imagem": "https://raw.githubusercontent.com/william-costa/wdev-urna-eletronica-resources/master/images/vegeta.png"
          }
        },
        "vereador":{
          "01234":{
            "nome": "Pikachu",
            "partido": "Pokemon",
            "imagem": "https://raw.githubusercontent.com/william-costa/wdev-urna-eletronica-resources/master/images/pikachu.png"
          },
          "08001":{
            "nome": "Goku",
            "partido": "Dragon Ball",
            "imagem": "https://raw.githubusercontent.com/william-costa/wdev-urna-eletronica-resources/master/images/goku.png"
          }
        }
      }
    }
  }
}
</script>

<style>
#app {
  background-color: var(--background-color);
  widows: 100%;
  height: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
}

.urna {
  width: 1000px;
  height: 500px;
  background-color: var(--ballot-box-background-color);
  padding: 30px;
  border-radius: 5px;
  display: flex;
  justify-content: space-between;
}
</style>

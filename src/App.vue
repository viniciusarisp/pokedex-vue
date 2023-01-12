<script>
//Lista de pokemons, busca e ordenação
import MainList from './components/MainList.vue';
//PopUp ao clicar no pokémon
import PopUpCard from './components/PopUpCard.vue';
//Footer da página
import MainFooter from './components/MainFooter.vue';
//Header da página
import MainHeader from './components/MainHeader.vue';

//Importa o metódo que gera a lista de pokemons
import { getList } from './methods/pokeapi';

export default {
  components: {
    MainFooter,
    MainHeader,
    PopUpCard,
    MainList,
  },
  name: 'App',
  data() {
    return {
      // Array de objetos com as informações dos Pokémon
      pokemons: [],
      // Controla a exibição da caixa de detalhes do Pokémon selecionado
      showCard: false,
      // Objeto com as informações do Pokémon selecionado
      selectedPokemon: {},
    }
  },
  methods: {
    //trata o pokemon recebido por MainList.vue
    setPokemon(pokemon) {
      this.selectedPokemon = pokemon;
      this.showCard = !this.showCard;
    },
    async getList(url){
      this.pokemons = await getList(url)
    }
  },
    mounted() {
      // Faz uma requisição à API de pokémons ao montar o componente
      const url = "https://pokeapi.co/api/v2/pokemon?limit=32&offset=0";
      this.getList(url);
    },
}
</script>

<template>
  <v-app>
    <MainHeader />
    <!-- Mainlist devolve o pokemon selecionado para app.vue -->
    <MainList :pokemons="pokemons" @selectPokemon="setPokemon($event)"/>
    <!-- PopUpCard Recebe o pokemon selecionado após ser definido na função setPokemon -->
    <PopUpCard :selectedPokemon="selectedPokemon" v-model="showCard" />
    <MainFooter />
  </v-app>
</template>

<style>
.grass {
  background-color: #8bd8517e;
}

.poison {
  background-color: #aa5ca083;
}

.fire {
  background-color: #fa544286;
}

.flying {
  background-color: #78a3ff79;
}

.water {
  background-color: #57acfb71;
}

.bug {
  background-color: #c3d2218e;
}

.normal {
  background-color: #babbad86;
}

.electric {
  background-color: #fbe13885;
}

.ground {
  background-color: #edc75583;
}

.fairy {
  background-color: #faafff81;
}

.fighting {
  background-color: #ad55417c;
}

.psychic {
  background-color: #f561b07c;
}

.rock {
  background-color: #cebc7275;
}

.ghost {
  background-color: #7671d070;
}

.ice {
  background-color: #95f2fe77;
}

.dragon {
  background-color: #8474ff73;
}

.dark {
  background-color: #8c67547c;
}

.steel {
  background-color: #c4c2da7a;
}
</style>

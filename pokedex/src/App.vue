
<script>
import axios from 'axios';

export default {
  name: 'App',

  components: {},

  data() {
    return {
      pokemons: [],
      busca: "",
      showCard: false,
      pageStart: 0,
      pageEnd: 16,
    }
  },

  methods: {
    getList(url) {
      //busca a página de pokemons
      axios.get(url).then((response) => {
        const requests = response.data.results.map((item) => axios.get(item.url));
        //aguarda todos os dados solicitados chegarem
        Promise.all(requests).then((results) => {
          //com todos os dados aqui, popula pokemons
          this.pokemons = results.map((itemResponse) => this.parsePokemon(itemResponse.data));
        });
      });
      //faz o sort por ID
      this.sortById(this.pokemons);
    },
    parsePokemon(pokemon){
      return {
        "id": pokemon.id,
        "name": pokemon.name,
        "height": pokemon.height/10,
        "weight": pokemon.weight/10,
        "abilities": pokemon.abilities.map((item)=>{return item.ability.name;}),
        "types": pokemon.types.map((item)=>{return item.type.name;}),
        "sprite": Math.floor(Math.random()*10) > 1 ? pokemon.sprites.front_default : pokemon.sprites.front_shiny, //hihi shiny
      }
    },
    sortById(pokemons) {
      pokemons.sort((a, b) => {
      return a.id - b.id;
      });
    },
    sortByName(pokemons) {
    pokemons.sort((a, b) => {
      if (a.name < b.name) {
        return -1;
      } else if (a.name > b.name) {
        return 1;
      } else {
        return 0;
      }
      });
    },
    show_pokemon(pokemon) {
      this.showCard = !this.showCard;
      this.selectedPokemon = pokemon;
    },
    changePage(param) {
      if(param === 'next' && this.pageEnd !== 480) {
        this.pageStart += 16
        this.pageEnd += 16
      } else if(param === 'prev' && this.pageStart !== 0) {
        this.pageStart -= 16
        this.pageEnd -= 16
      }
    }
  },

  mounted() {
    var url = "https://pokeapi.co/api/v2/pokemon?limit=64&offset=0";
    this.getList(url);
  },
  computed: {
    // Quando a busca muda, recalcula-se o filtro e retorna pokesfiltrados()
    pokesFiltrados() {
    const search = this.busca.toLowerCase();
    return this.pokemons.filter((item) => {
      const name = item.name.toLowerCase();
      const type = item.types.map((type) => type.toLowerCase());
      return name.includes(search) || type.includes(search);
    });
  },
  }
}
</script>


<template>
  <v-app>
    <v-main>
      <v-btn @click="sortByName(pokemons)">order by name</v-btn>
      <v-btn @click="sortById(pokemons)">order by ID</v-btn>
      <v-btn @click="changePage('next')">next page</v-btn>
      <v-btn @click="changePage('prev')">previous page</v-btn>
        <v-card>
          <v-text-field
            v-model="busca"
            label="Buscar Pokemon"
            placeholder="pikachu"
            solo
          ></v-text-field>
          <v-row>
            <v-col sm="4" md="3" lg="1" v-for="pokemon in pokesFiltrados.slice(this.pageStart, this.pageEnd)" :key="pokemon.name" class="">
              <v-card class="pokemon-card ma-1" :value="pokemon.name" v-on:click="show_pokemon(pokemon)">
                <v-container class="text-center">
                  <v-row class="mx-2 justify-center align-items-center flex-column">
                    <h2 class="text-capitalize"> {{ pokemon.name }}</h2>
                    <img
                      :src="pokemon.sprite"
                      :alt="pokemon.name"
                      height="220"
                    />
                  </v-row>
                  <v-row v-for="type in pokemon.types" class="text-center">
                    <span :class=type> {{ type }} </span>
                  </v-row>
                </v-container>
                
              </v-card>
            </v-col>
          </v-row>
        </v-card>
    </v-main>
    <v-dialog v-model="showCard" width="300">
      <v-card class="pokedex-card text-center pa-5">
        <img :src="selectedPokemon.sprite" :alt="selectedPokemon.name"/>
        <h2 class="text-capitalize"> {{ selectedPokemon.name }}</h2>

        <p class="">types: {{ selectedPokemon.types.join(', ') }}</p>

        <p class="">abilities: {{ selectedPokemon.abilities.join(', ') }}</p>

        <div class="stats">
          <p>weight: {{ selectedPokemon.weight }} kg</p>
          <p>height: {{ selectedPokemon.height }} m</p>
        </div>
      </v-card>
    </v-dialog>
  </v-app>
</template>

<style>
.water {

}
.bug {
  
}
</style>

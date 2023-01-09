
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
        "height": pokemon.height,
        "weight": pokemon.weight,
        "abilities": pokemon.abilities.map((item)=>{return item.ability.name;}),
        "types": pokemon.types.map((item)=>{return item.type.name;}),
        "sprite": Math.floor(Math.random()*20) > 1 ? pokemon.sprites.front_default : pokemon.sprites.front_shiny, //hihi shiny
      }
    },
    sortById(pokemons) {
      pokemons.sort((a, b) => {
      return b.id - a.id;
      });
    },
    show_pokemon() {
      this.showCard = !this.showCard
    }
  },

  mounted() {
    var url = "https://pokeapi.co/api/v2/pokemon?limit=50&offset=0";
    this.getList(url);
  },
  computed: {
    // Quando a busca muda, recalcula-se o filtro e retorna pokesfiltrados()
    pokesFiltrados() {
      return this.pokemons.filter((item) => {
        return item.name.includes(this.busca)
      }) 
    }
  }
}
</script>


<template>
  <v-app>
    <v-main>
      <v-container>
        <v-card>
          <v-text-field
          v-model="busca"
          label="Buscar Pokemon"
          placeholder="pikachu"
          solo
          >
          </v-text-field>
          <v-row>
            <v-col cols="3" v-for="pokemon in pokesFiltrados" :key="pokemon.name">
              <v-card :value="pokemon.name" v-on:click="show_pokemon(pokemon)">
                <v-container>
                  <v-row class="mx-2 d-flex justify-center">
                    <img 
                      :src="pokemon.sprite" 
                      :alt="pokemon.name"
                      max-width="100%"
                    />
                    <h2 class="text-center text-capitalize"> {{ pokemon.name }}</h2>
                  </v-row>
                </v-container>
              </v-card>
            </v-col>
          </v-row>
        </v-card>
      </v-container>
    </v-main>
    <v-dialog
      v-model="showCard"
      width="500"
    >
      <v-card>
        <v-container></v-container>
       </v-card>
    </v-dialog>
  </v-app>
</template>

<style>
</style>


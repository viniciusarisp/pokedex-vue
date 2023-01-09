
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
    show_pokemon(pokemon) {
      this.showCard = !this.showCard;
      this.selectedPokemon = pokemon;
    }
  },

  mounted() {
    var url = "https://pokeapi.co/api/v2/pokemon?limit=50&offset=0";
    this.getList(url);
  },
  computed: {
    // Quando a busca muda, recalcula-se o filtro e retorna pokesfiltrados()
    pokesFiltrados() {
      const search = this.busca.toLowerCase();
      return this.pokemons.filter((item) => {
        const name = item.name.toLowerCase();
        return name.includes(search);
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
          <v-row>
            <v-col cols="12">
              <v-card class="pokemon-card">
                <img :src="selectedPokemon.sprite" :alt="selectedPokemon.name"/>
                <h2 class="text-center text-capitalize"> {{ selectedPokemon.name }}</h2>
                <p class="text-center">Tipos: {{ selectedPokemon.types.join(', ') }}</p>
                <p class="text-center">Habilidades: {{ selectedPokemon.abilities.join(', ') }}</p>
                <p class="text-center">Peso: {{ selectedPokemon.weight }}</p>
                <p class="text-center">Altura: {{ selectedPokemon.height }}</p>
              </v-card>
            </v-col>
          </v-row>
    </v-dialog>
  </v-app>
</template>

<style>
  .pokemon-card {
    display: grid;
    grid-template-columns: repeat(2, 1fr);
    background-color: #f8f8f8;
    border-radius: 5px;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
    font-family: sans-serif;
  }
  .pokemon-card img {
    width: 200px;
    height: 200px;
    object-fit: cover;
    border-radius: 5px;
  }
  .pokemon-card h2 {
    font-size: 24px;
    color: #333;
    margin: 10px 0;
    text-transform: uppercase;
  }
  .pokemon-card p {
    font-size: 16px;
    color: #666;
    margin: 10px 0;
  }
</style>

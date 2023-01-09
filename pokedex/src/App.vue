
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
      const type = item.types.map((t) => t.toLowerCase());
      return name.includes(search) || type.includes(search);
    });
  },
  }
}
</script>


<template>
  <v-app>
    <v-main>
      <v-btn @click="sortByName(pokemons)">Ordenar por nome</v-btn>
      <v-btn @click="sortById(pokemons)">Ordenar por ID</v-btn>
        <v-card>
          <v-text-field
            v-model="busca"
            label="Buscar Pokemon"
            placeholder="pikachu"
            solo
          ></v-text-field>
          <v-row>
            <v-col cols="4" sm="3" md="3" lg="4" v-for="pokemon in pokesFiltrados" :key="pokemon.name" class="">
              <v-card class="pokemon-card ma-3" :value="pokemon.name" v-on:click="show_pokemon(pokemon)">
                <v-container>
                  <v-row class="mx-2 d-flex justify-center align-items-center">
                    <img
                      :src="pokemon.sprite"
                      :alt="pokemon.name"
                      max-width="100%"
                    />
                    <h2 class="text-center text-capitalize"> {{ pokemon.name }}</h2>
                  </v-row>
                </v-container>
                <p class="text-center type-text">{{ pokemon.types.join(', ') }}</p>
              </v-card>
            </v-col>
          </v-row>
        </v-card>
    </v-main>
    <v-dialog v-model="showCard" width="300">
      <v-card class="pokedex-card text-center pa-5">
        <img :src="selectedPokemon.sprite" :alt="selectedPokemon.name"/>
        <h2 class="text-capitalize"> {{ selectedPokemon.name }}</h2>
        <p class="">Tipos: {{ selectedPokemon.types.join(', ') }}</p>
        <div class="stats">
          <p>Peso: {{ selectedPokemon.weight }}</p>
          <p>Altura: {{ selectedPokemon.height }}</p>
        </div>
      </v-card>
    </v-dialog>
  </v-app>
</template>

<style>

</style>

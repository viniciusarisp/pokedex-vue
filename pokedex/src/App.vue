
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
        var results = response.data.results;
        results.forEach((item)=>{
          //busca todos os dados de cada pokemon na página
          axios.get(item.url).then((itemResponse) => {
            this.pokemons.push(this.parsePokemon(itemResponse.data))
          })
        });
      });
      this.pokemons = this.sortById(this.pokemons);
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
      var length = pokemons.length;
      for (var i = 1; i < length; i++) {
        var current = pokemons[i];
        var j = i - 1;
        while ((j > -1) && (current.id < pokemons[j].id)) {
            pokemons[j + 1] = pokemons[j];
            j--;
        }
        pokemons[j+1] = current;
      }
      return pokemons;
    },
    show_pokemon() {
      this.showCard = !this.showCard
    }
  },

  mounted() {
    var url = "https://pokeapi.co/api/v2/pokemon?limit=20&offset=0";
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


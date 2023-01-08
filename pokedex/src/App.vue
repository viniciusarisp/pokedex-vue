<script>
import axios from 'axios';

export default {
  name: 'App',

  components: {},

  data() {
    return {
      pokemons: [],
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
      this.pokemons.sort(this.sortById())
      console.log(this.pokemons)
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
    sortById() {
      return function (pokemon1, pokemon2) {
        if (pokemon1.id < pokemon2.id) return -1;
        if (pokemon1.id > pokemon2.id) return 1;
        return 0;
      };
    },
  },

  mounted() {
    var url = "https://pokeapi.co/api/v2/pokemon?limit=20&offset=0";
    this.getList(url);
  }
}
</script>


<template>
  <v-app>
    <v-main>
              <v-container>
                <v-card>
                  <v-row>
                    <v-col cols="3" v-for="pokemon in pokemons" :key="pokemon.name">
                      <v-card :value="pokemon.name">
                        <v-container>
                          <v-row class="mx-2 d-flex justify-center">
                            <img 
                            :src="pokemon.sprite" 
                            :alt="pokemon.name" 
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
  </v-app>
</template>


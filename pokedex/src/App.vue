<template>
  <v-app>
    <v-main>
      <v-container>
        <v-card>
            {{ pokemons }}
        </v-card>
      </v-container>
    </v-main>
  </v-app>
</template>

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

  mounted(){
    axios.get("https://pokeapi.co/api/v2/pokemon?limit=100000&offset=0").then((response) => {
      this.pokemons = response.data.results
    })
  }
}
</script>

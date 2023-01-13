<script>
  export default {
    name: 'PopUpCard',
    props: ['selectedPokemon'],
    components: {},
    data: () => ({}), 
  }
</script>

<template>
  <v-dialog width="500">
    <v-card class="pokedex-card text-center pa-5 d-flex">
      <h1 class="text-capitalize my-2"> {{ selectedPokemon.name }}</h1>
      <img 
        :src="selectedPokemon.sprite" 
        :alt="selectedPokemon.name" 
        width="300" 
        class="block ma-auto d-block mb-4" 
      />
      <v-row 
        v-for="(type) in selectedPokemon.types"
      >
        <v-col :class="type" 
          class="rounded-pill my-1 mx-8 text-uppercase font-weight-black" >{{ type }}
        </v-col>
      </v-row>
      <v-row class="bg-red">
        <v-col>
          <p class="">abilities: {{ selectedPokemon.abilities.join(', ') }}</p>
        </v-col>
        <v-col>
          <p>weight: {{ selectedPokemon.weight }} kg</p>
          <p>height: {{ selectedPokemon.height }} m</p>
        </v-col>
      </v-row>
    </v-card>
  </v-dialog>
</template>

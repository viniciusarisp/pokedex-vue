<script>
export default {
      name: 'popUpCard',
      props: ['selectedPokemon'],
      components: {},

      data: () => ({}),
}

</script>
<template>
      <v-dialog v-model="showCard" width="300">
            <v-card class="pokedex-card text-center pa-5 d-flex">
                <img :src="selectedPokemon.sprite" :alt="selectedPokemon.name" width="300"
                    class="inline-block ma-auto" />

                <h2 class="text-capitalize my-2"> {{ selectedPokemon.name }}</h2>

                <v-row v-for="type in selectedPokemon.types" :key="type">
                    <v-col :class="type" class="rounded-pill">{{ type }}</v-col>
                </v-row>
                <v-row>
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
<style>
</style>
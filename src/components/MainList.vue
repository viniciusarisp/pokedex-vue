<script>
import { onMounted } from 'vue';

export default {
  name: 'MainList',
  props: ['pokemons'],
  components: {},

  data: () => ({
    url: 'https://pokeapi.co/api/v2/pokemon?limit=151&offset=0',
    // String com o valor geração selecionada
    selectedGeneration: '1ª Geração (1-151)',
    //Array com os ranges de cada geração de pokemon
    generations: [],
    // Objeto com as informações do Pokémon selecionado
    selectedPokemon: {},
    // Valor da busca
    busca: "",
    //Páginas
    pages: {
        // Índice inicial do array de Pokémon a ser exibido na página
        pageStart: 0,
        // Índice final do array de Pokémon a ser exibido na página
        pageEnd: 20,
        //Página atual
        page: 1,
      }
  }),

  methods: {
    sortById(pokemons) {
      // Ordena a lista de pokémons pelo ID
      pokemons.sort((a, b) => a.id - b.id);
    },
    sortByName(pokemons) {
      // Ordena a lista de pokémons pelo nome
      pokemons.sort((a, b) => {
        if (a.name < b.name) return -1;
        if (a.name > b.name) return 1;
        return 0;
      });
    },
    showPokemon(pokemon) {
      // Atualiza o pokémon selecionado
      this.selectedPokemon = pokemon;
      //Passa para o app.vue o pokemon selecionado 
      this.$emit('selectPokemon', this.selectedPokemon)
    },
    changePage() {
      //Defino o range da página selecionada
      this.pages.pageStart = 20 * (this.pages.page - 1);
      this.pages.pageEnd = 20 * this.pages.page;
    },
    setGeneration(selectedGeneration) {
      let genNumbers = selectedGeneration.match(/(\d+)/g)
      console.log(genNumbers)
      this.$emit('urlAPI', this.url)
    }
  },
  computed: {
    pokesFiltrados() {
      // Filtra a lista de pokémons com base na busca
      const search = this.busca.toLowerCase();
      return this.pokemons.filter(item => {
        const name = item.name.toLowerCase();
        const type = item.types.map(type => type.toLowerCase());
        this.pages.page = 1;
        return name.includes(search) || type.includes(search);
      });
    }
  },
  beforeMount() {
    this.setGeneration(this.selectedGeneration)
  }
}

</script>
<template>
  <v-main class="">
    <v-text-field 
      v-model="busca" 
      class="" 
      label="Search Pokemon"
      placeholder='"Pikachu", "Grass"' 
      solo>
    </v-text-field>
    <v-card>
      <v-row>
        <v-col class="d-flex justify-start align-center">
          <v-btn 
            @click="sortByName(pokemons)" 
            class="mx-3">
            order by name
          </v-btn>
          <v-btn 
            @click="sortById(pokemons)" 
            class="mx-3">
            order by ID
          </v-btn>
        </v-col>
        <v-col>
          <v-select
            v-model="selectedGeneration"
            chips
            label="Geração"
              :items="['1ª Geração (1-151)', '2ª Geração (151-251)', '3ª Geração (251-386)', '4ª Geração (386-493)', '5ª Geração (493-649)', '6ª Geração (649-721)', '7ª Geração (721-809)','8ª Geração (809-905)']"
            variant="underlined"
            @update:modelValue="setGeneration(selectedGeneration)"
          ></v-select>
        </v-col>
        <v-col 
        class="d-flex justify-end">
          <div class="">
            <v-pagination 
              v-model="pages.page" 
              :length="Math.ceil(pokesFiltrados.length / 20)" 
              :total-visible="3"
              @click="changePage()">
            </v-pagination>
          </div>
        </v-col>
      </v-row>
      <v-row  
        class="pa-1 ma-4"
        xs=""
        sm=""
        md=""
        lg=""
        xl=""
      >
        <v-col  
          v-for="pokemon in pokesFiltrados.slice(this.pages.pageStart, this.pages.pageEnd)"
          :key="pokemon.name" 
          class="" 
          elevation="4">
          <v-card 
            class='pokemon-card'  
            width="200"
            height="250"
            v-on:click="showPokemon(pokemon)" 
            elevation="8">
            <v-container :class="pokemon.types[0]" class="">
              <v-row class="d-flex flex-column justify-center" no-gutters>
                <h2 class="text-capitalize text-center"> {{ pokemon.name }} </h2>
                <v-img 
                  class=""
                  :aspect-ratio="1/1"
                  :src="pokemon.sprite" 
                  :alt="pokemon.name"
                  cover
                  height="170">
                </v-img>
              </v-row>
              <p class="font-weight-medium text-left"> #{{ pokemon.id.toString().padStart(3,'0') }}</p>
            </v-container> 
          </v-card>
        </v-col>
      </v-row>
    </v-card>
  </v-main>
</template>
<script>
import { pokeapi } from "@/api/pokeapi";

var poke_data = {};

export default {
  name: "App",

  data() {
    return {
      poke_pagina: {}, //define a listagem de cada página
      poke_card_data: {}, //Dados completos do poke
      pokemonDados: {},
      pokemon_ID: "",
    };
  },

  methods: {
    async busca_pagina(
      param = "https://pokeapi.co/api/v2/pokemon/?offset=0&limit=20"
    ) {
      var pag = {};
      try {
        const pagina_buscada = await fetch(param);
        const pagina = await pagina_buscada.json();
        pag = pagina.results;
      } catch (error) {
        console.log("Página não encontrada");
      }
      pag.forEach(async (element) => {
        const cada_poke = await fetch(`${element.url}`);
        const poke = await cada_poke.json();
        poke_data += poke;
        console.log(poke);
      });
      // console.log(poke_data);
    },
    async busca_pokemon() {
      try {
        const pokemon_buscado = await fetch(`${pokeapi}/${this.pokemon_ID}`);
        const pokemon = await pokemon_buscado.json();
        this.pokemonDados = pokemon;

        console.log(pokemon);
      } catch (error) {
        alert("Pokemon não encontrado!");
      }
    },
  },
  beforeMount() {
    this.busca_pagina();
  },
};
</script>

<template>
  <header class="header">
    <label for="">
      Digite o nome do pokemon ou ID:
      <input type="text" v-model="pokemon_ID" />
      <!-- v-model vincula a variável em Dados()-->
      <button @click="busca_pokemon()" class="botao_busca">
        Buscar pokemon!
      </button>
    </label>
  </header>
  <main class="main" v-if="Object.entries(pokemonDados).length > 0">
    <section class="pokemon_card">
      <div class="nome_foto">
        <img
          :src="pokemonDados.sprites.front_default"
          :alt="pokemonDados.name"
        />
        <h1 class="pokemon_nome">{{ pokemonDados.name }}</h1>
      </div>
      <ul class="tipo">
        <h2>Tipo:</h2>
        <li
          v-for="(type, index) in pokemonDados.types"
          :key="index"
          :class="type.type.name"
        >
          <span>{{ type.type.name }}</span>
        </li>
      </ul>
      <ul class="habilidades">
        <h2>Habilidades:</h2>
        <li v-for="(habilidade, index) in pokemonDados.abilities" :key="index">
          <span>{{ habilidade.ability.name }}</span>
        </li>
      </ul>
    </section>
  </main>
  <ul>
    <h2>Lista de pokemons</h2>
    <li v-for="(poke, index) in poke_card_data" :key="index">
      <div>
        <span>{{ poke.name }}</span>
      </div>
    </li>
  </ul>
</template>

<style scoped></style>

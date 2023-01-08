<script>
import { pokeapi } from "@/api/pokeapi";

export default {
  name: "App",

  data() {
    return {
      pokemonDados: {},
      pokemon_ID: "",
    };
  },

  methods: {
    async busca_pokemon() {
      try {
        const pokemon_buscado = await fetch(`${pokeapi}/${this.pokemon_ID}`);
        const pokemon = await pokemon_buscado.json();
        this.pokemonDados = pokemon;

        console.log(pokemon);

        return pokemon;
      } catch (error) {
        alert("Pokemon não encontrado!");
      }
    },
  },
  beforeMount() {},
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
    <li>
      <div>
        <img src="" alt="foto-poke" />
        <span>nome</span>
      </div>
    </li>
  </ul>
</template>

<style scoped></style>

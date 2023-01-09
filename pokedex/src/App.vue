
<script>
//biblioteca axios para fazer requisições HTTP
import axios from 'axios';

export default {
    name: 'App',
    // Componentes filhos
    components: {},
    // Dados do componente
    data() {
        return {
            // Array de objetos com as informações dos Pokémon
            pokemons: [],
            // Valor da busca
            busca: "",
            // Controla a exibição da caixa de detalhes do Pokémon selecionado
            showCard: false,
            // Índice inicial do array de Pokémon a ser exibido na página
            pageStart: 0,
            // Índice final do array de Pokémon a ser exibido na página
            pageEnd: 16,
            // Objeto com as informações do Pokémon selecionado
            selectedPokemon: {}
        }
    },
    methods: {
        async getList(url) {
            try {
                // Faz uma requisição GET à URL passada como parâmetro
                const response = await axios.get(url);
                // Faz uma requisição GET para cada item da lista retornada pela primeira requisição
                const requests = response.data.results.map(item => axios.get(item.url));
                // Aguarda a conclusão de todas as requisições
                const results = await Promise.all(requests);
                // Atualiza a lista de pokémons com os dados obtidos nas requisições
                this.pokemons = results.map(itemResponse => this.parsePokemon(itemResponse.data));
                // Ordena a lista de pokémons pelo ID
                this.sortById(this.pokemons);
            } catch (error) {
                console.error(error);
            }
        },
        parsePokemon(pokemon) {
            return {
                "id": pokemon.id,
                "name": pokemon.name,
                "height": pokemon.height / 10,
                "weight": pokemon.weight / 10,
                "abilities": pokemon.abilities.map(item => item.ability.name),
                "types": pokemon.types.map(item => item.type.name),
                "sprite": Math.random() > 0.1 ? pokemon.sprites.front_default : pokemon.sprites.front_shiny,
            }
        },
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
            // Alterna a exibição da "card" de um pokémon
            this.showCard = !this.showCard;
            // Atualiza o pokémon selecionado
            this.selectedPokemon = pokemon;
        },
        changePage(param) {
            if (param === 'next' && this.pageEnd !== 480) {
                // Avança para a próxima página de pokémons
                this.pageStart += 16;
                this.pageEnd += 16;
            } else if (param === 'prev' && this.pageStart !== 0) {
                // Volta para a página anterior de pokémons
                this.pageStart -= 16;
                this.pageEnd -= 16;
            }
        }
    },
    computed: {
        pokesFiltrados() {
            // Filtra a lista de pokémons com base na busca
            const search = this.busca.toLowerCase();
            return this.pokemons.filter(item => {
                const name = item.name.toLowerCase();
                const type = item.types.map(type => type.toLowerCase());
                return name.includes(search) || type.includes(search);
            });
        }
    },
    mounted() {
        // Faz uma requisição à API de pokémons ao montar o componente
        const url = "https://pokeapi.co/api/v2/pokemon?limit=480&offset=0";
        this.getList(url);
    },
}
</script>


<template>
    <v-app>
        <v-main class="pa-10">
            <v-text-field v-model="busca" label="Search Pokemon" placeholder='"Pikachu", "Grass"' solo></v-text-field>
            <v-card>
                <v-row>
                    <v-col class="d-flex justify-start">
                        <v-btn @click="sortByName(pokemons)" class="mx-3">order by name</v-btn>
                        <v-btn @click="sortById(pokemons)" class="mx-3">order by ID</v-btn>
                    </v-col>
                    <v-col class="d-flex justify-end">
                        <v-btn @click="changePage('prev')" class="mx-3">previous page</v-btn>
                        <v-btn @click="changePage('next')">next page</v-btn>
                    </v-col>
                </v-row>
                <v-row>
                    <v-col sm="5" md="3" lg="4" v-for="pokemon in pokesFiltrados.slice(this.pageStart, this.pageEnd)"
                        :key="pokemon.name" class="">
                        <v-card class="pokemon-card ma-1" :value="pokemon.name" v-on:click="showPokemon(pokemon)"
                            elevation="2">
                            <v-container class="text-center">
                                <v-row class="mx-2 justify-center align-items-center flex-column">
                                    <h2 class="text-capitalize"> {{ pokemon.name }}</h2>
                                    <v-img :src="pokemon.sprite" :alt="pokemon.name" height="200"></v-img>
                                </v-row>
                            </v-container>
                        </v-card>
                    </v-col>
                </v-row>
            </v-card>
        </v-main>
        <v-dialog v-model="showCard" width="300">
            <v-card class="pokedex-card text-center pa-5">
                <img :src="selectedPokemon.sprite" :alt="selectedPokemon.name" />
                <h2 class="text-capitalize"> {{ selectedPokemon.name }}</h2>

                <p class="">types: {{ selectedPokemon.types.join(', ') }}</p>

                <p class="">abilities: {{ selectedPokemon.abilities.join(', ') }}</p>

                <div class="stats">
                    <p>weight: {{ selectedPokemon.weight }} kg</p>
                    <p>height: {{ selectedPokemon.height }} m</p>
                </div>
            </v-card>
        </v-dialog>
    </v-app>
</template>

<style>
.pokemon-card {
    height: 80%;
    width: 80%;
}
.tipos-poke span {
    border: 2px solid black;
}

.grass {
    background-color: #8cd851;
}

.poison {
    background-color: #aa5ca0;
}

.fire {
    background-color: #fa5542;
}

.flying {
    background-color: #78a3ff;
}

.water {
    background-color: #57adfb;
}

.bug {
    background-color: #c3d221;
}

.normal {
    background-color: #babbad;
}

.electric {
    background-color: #fbe238;
}

.ground {
    background-color: #edc655;
}

.fairy {
    background-color: #faafff;
}

.fighting {
    background-color: #ad5541;
}

.psychic {
    background-color: #f561af;
}

.rock {
    background-color: #cebc72;
}

.ghost {
    background-color: #7571d0;
}

.ice {
    background-color: #95f1fe;
}

.dragon {
    background-color: #8574ff;
}

.dark {
    background-color: #8c6754;
}

.steel {
    background-color: #c4c2da;
}
</style>

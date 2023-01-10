<script>
import popUpCard from './components/popupcard.vue';
//footer da página
import footerVue from './components/footerVue.vue';
//header da página
import headerVue from './components/header.vue';
//biblioteca axios para fazer requisições HTTP
import axios from 'axios';

export default {

    components: {
        footerVue,
        headerVue,
        popUpCard,
    },
    data() {
        return {
            // Array de objetos com as informações dos Pokémon
            pokemons: [],
            // Valor da busca
            busca: "",
            // Controla a exibição da caixa de detalhes do Pokémon selecionado
            showCard: false,
            // Objeto com as informações do Pokémon selecionado
            selectedPokemon: {},
            //Páginas:
            pages: {
                // Índice inicial do array de Pokémon a ser exibido na página
                pageStart: 0,
                // Índice final do array de Pokémon a ser exibido na página
                pageEnd: 16,
                //Página atual
                page: 1,
            }
        }
    },
    name: 'App',
    // Componentes filhos

    // Dados do componente
    data() {
        return {
            // Array de objetos com as informações dos Pokémon
            pokemons: [],
            // Valor da busca
            busca: "",
            // Controla a exibição da caixa de detalhes do Pokémon selecionado
            showCard: false,
            // Objeto com as informações do Pokémon selecionado
            selectedPokemon: {},
            //Páginas:
            pages: {
                // Índice inicial do array de Pokémon a ser exibido na página
                pageStart: 0,
                // Índice final do array de Pokémon a ser exibido na página
                pageEnd: 16,
                //Página atual
                page: 1,
            }
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
        changePage() {
            this.pages.pageStart = 16 * (this.pages.page - 1);
            this.pages.pageEnd = 16 * this.pages.page;
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
    mounted() {
        // Faz uma requisição à API de pokémons ao montar o componente
        const url = "https://pokeapi.co/api/v2/pokemon?limit=32&offset=0";
        this.getList(url);
    },
}
</script>


<template>
    <v-app>
        <headerVue />
        <v-main class="ma-10">
            <v-text-field v-model="busca" class="font-weight-bold white--text" label="Search Pokemon"
                placeholder='"Pikachu", "Grass"' solo></v-text-field>
            <v-card>
                <v-row>
                    <v-col class="d-flex justify-start">
                        <v-btn @click="sortByName(pokemons)" class="mx-3">order by name</v-btn>
                        <v-btn @click="sortById(pokemons)" class="mx-3">order by ID</v-btn>
                    </v-col>
                    <v-col class="d-flex justify-end">
                        <div class="text-center">
                            <v-pagination v-model="pages.page" :length="Math.ceil(pokesFiltrados.length / 16)"
                                :total-visible="3" @click="changePage()"></v-pagination>
                        </div>
                    </v-col>
                </v-row>
                <v-row>
                    <v-col sm="5" md="3" lg="3"
                        v-for="pokemon in pokesFiltrados.slice(this.pages.pageStart, this.pages.pageEnd)"
                        :key="pokemon.name" class="" elevation="4">
                        <v-card class='pokemon-card' v-on:click="showPokemon(pokemon)" elevation="2">
                            <v-container :class="pokemon.types[0]">
                                <v-row class="mx-2 justify-center align-items-center flex-column">
                                    <h2 class="text-capitalize text-center"> {{ pokemon.name }}</h2>
                                    <v-img :src="pokemon.sprite" :alt="pokemon.name" height="200"></v-img>
                                </v-row>
                            </v-container>
                        </v-card>
                    </v-col>
                </v-row>
            </v-card>
        </v-main>
        <popUpCard />
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
        <footerVue />
    </v-app>
</template>

<style>
.pokedex-card {
    width: 110%;
    height: 110%;
}

.pokedex-card>* {
    padding: 5px;
}

.pokemon-card {
    height: 100%;
    width: 100%;
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

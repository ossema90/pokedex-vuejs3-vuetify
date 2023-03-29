<template>
  <v-container>
    <v-row align="center" justify="center">
      <v-col cols="12">
        <h1 class="text-center">Pokedex</h1>
      </v-col>
      <v-col cols="6" class="mt-4">
        <v-text-field label="Search Pokemon" v-model="searchTerm"></v-text-field>
      </v-col>
      <v-col cols="12">
        <v-row justify="center">
          <v-col v-for="pokemon in filteredPokemon" :key="pokemon.id" cols="6" sm="4" md="3" lg="2" class="mb-4">
            <v-card outlined @click="handlePokemonClick(pokemon.id)">
              <v-img :src="pokemon.image" :alt="pokemon.name"></v-img>
              <v-card-title>{{ pokemon.name }}</v-card-title>
            </v-card>
          </v-col>
        </v-row>
      </v-col>
      <v-col v-if="selectedPokemon != null" cols="6" class="mt-4">
        <v-card>
          <v-img :src="selectedPokemon.image" :alt="selectedPokemon.name"></v-img>
          <v-card-title>{{ selectedPokemon.name }}</v-card-title>
          <v-card-text>
            <p>Height: {{ selectedPokemon.height }}</p>
            <p>Weight: {{ selectedPokemon.weight }}</p>
          </v-card-text>
        </v-card>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
import { ref, computed, onMounted } from 'vue';

export default {
  setup() {
    const searchTerm = ref('');
    const pokemonList = ref([]);
    const selectedPokemonId = ref(null);
    const selectedPokemon = ref(null);

    const filteredPokemon = computed(() => {
      return pokemonList.value.filter(pokemon => pokemon.name.includes(searchTerm.value.toLowerCase()));
    });

    const getPokemonList = async () => {
      try {
        const response = await fetch('https://pokeapi.co/api/v2/pokemon?limit=150');
        const data = await response.json();
        pokemonList.value = data.results.map((pokemon, index) => {
          return {
            id: index + 1,
            name: pokemon.name,
            image: `https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/${index + 1}.png`
          };
        });
      } catch (error) {
        console.error(error);
      }
    };

    const getSelectedPokemon = async (pokemonId) => {
      try {
        const response = await fetch(`https://pokeapi.co/api/v2/pokemon/${pokemonId}`);
        const data = await response.json();
        selectedPokemon.value = {
          id: pokemonId,
          name: data.name,
          image: `https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/${pokemonId}.png`,
          height: data.height,
          weight: data.weight
        };
      } catch (error) {
        console.error(error);
      }
    };

    const handlePokemonClick = (pokemonId) => {
      selectedPokemonId.value = pokemonId;
      getSelectedPokemon(pokemonId);
    };

    onMounted(getPokemonList);

    return {
      searchTerm,
      pokemonList,
      selectedPokemon,
      filteredPokemon,
      handlePokemonClick
    };
  }
};
</script>

<style>
.v-card {
  cursor: pointer;
}
</style>

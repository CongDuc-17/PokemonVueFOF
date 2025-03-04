<script setup>
import { ref, computed } from "vue";
import pokemon from "@/components/pokemonItem.vue";
import pokemonDetail from "@/components/pokemonDetail.vue";

let pokemons = [];
const offset = ref(0);
const number_of_render = 12;
const filteredPokemons = ref([]);
const renderPokemons = computed(() =>
  filteredPokemons.value.slice(0, offset.value + number_of_render)
);
async function cFetch(URL) {
  const response = await fetch(URL);
  return await response.json();
}
async function fetchPokemons() {
  const data = await cFetch("https://pokeapi.co/api/v2/pokemon/?offset=0&limit=898");
  pokemons = data.results;
  filteredPokemons.value = pokemons;
}
fetchPokemons();

function handleLoadMore() {
  offset.value += number_of_render;
}
function handleSearch(event) {
  filteredPokemons.value = pokemons.filter((pokemon) => {
    return pokemon.name.includes(event.target.value);
  });
  offset.value = 0;
}
//select pokemon
const selectedPokemon = ref(null);
const selectedDesc = ref("");
function handleSelectPokemon(pokemonData, pokemonDesc) {
  selectedPokemon.value = pokemonData;
  selectedDesc.value = pokemonDesc;
}

function handleBack() {
  selectedPokemon.value = null;
}
</script>
<template>
  <div class="container">
    <pokemonDetail
      v-if="selectedPokemon"
      :pokemon="selectedPokemon"
      :pokemon_desc="selectedDesc"
      @back="handleBack"
    />
    <template v-else>
      <div class="header">
        <h1 class="heading">Pokemon API</h1>
        <input
          class="search"
          type="text"
          placeholder="Enter your keyword here..."
          style="width: 100%"
          @input="handleSearch"
        />
      </div>
      <div class="items">
        <pokemon
          v-for="pokemon in renderPokemons"
          :key="pokemon.name"
          :url="pokemon.url"
          @select-pokemon="handleSelectPokemon"
        />
      </div>

      <button
        v-show="filteredPokemons.length > number_of_render"
        class="button-load"
        @click="handleLoadMore"
      >
        Load More
      </button>
    </template>
  </div>
</template>

<style>
.container {
  display: flex;
  justify-content: center;
  width: 100%;
  color: black;
}
.header {
  display: flex;
  flex-direction: column;
  align-items: center;
}
.heading {
  margin-block: 25px;
}
.search {
  width: 100%;
  padding: 20px;
  border: none;
  border-radius: 30px;
  outline: 1px solid #00000036;
  box-shadow: #64646f33 0 7px 29px;
  font-size: 16px;
  transition: all 0.2s ease;
  margin-bottom: 50px;
}
.items {
  display: grid;
  grid-template-columns: repeat(6, 1fr);
  transition: 0.5s;
  gap: 5px;
  .item {
    display: flex;
    flex-direction: column;
    align-items: center;
    padding: 5px 5px;
    cursor: pointer;
    border-radius: 10px;
    box-shadow: #f4f4f4 0 7px 29px;
    text-transform: capitalize;
    transition: 0.3s;
  }
  .item:hover {
    box-shadow: #39393f33 0 7px 29px;
    transition: 0.3s;
  }
}
.button-load {
  margin-top: 50px;
  margin-bottom: 50px;
  cursor: pointer;
  padding: 20px 25px;
  border: none;
  border-radius: 10px;
  font-size: 16px;
  color: #fff;
  background-color: #ff4d4f;
}
</style>

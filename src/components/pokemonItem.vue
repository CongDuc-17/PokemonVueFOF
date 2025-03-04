<script setup>
import { ref, onMounted } from "vue";
import { useRouter } from "vue-router";
import { watch } from "vue";

const props = defineProps(["url"]);
defineEmits(["select-pokemon"]);
const router = useRouter();
async function cFetch(URL) {
  const response = await fetch(URL);
  return await response.json();
}
const pokemon = ref({});
const pokemon__desc = ref({});
cFetch(props.url).then((data) => {
  pokemon.value = data;
});
async function fetchPokemon() {
  pokemon.value = await cFetch(props.url);

  const speciesData = await cFetch(
    `https://pokeapi.co/api/v2/pokemon-species/${pokemon.value.id}/`
  );
  pokemon__desc.value =
    speciesData.flavor_text_entries
      .find((entry) => entry.language.name === "en")
      ?.flavor_text.replace(/[\n\f]/g, " ") || "";
}

function viewPokemonDetail() {
  sessionStorage.setItem("selectedPokemon", JSON.stringify(pokemon.value));
  router.push("/" + pokemon.value.name);
}
onMounted(() => {
  fetchPokemon();
});
watch(() => props.url, fetchPokemon);
const typeColors = {
  grass: "#78cd54",
  poison: "#a33ea1",
  fire: "#ff421c",
  flying: "#a98ff3",
  water: "#6390f0",
  bug: "#a6b91a",
  normal: "#a8a77a",
  electric: "#f7d02c",
  ground: "#e2bf65",
  fairy: "#d685ad",
  fighting: "#c22e28",
  psychic: "#f95587",
  rock: "#f95587",
  ghost: "#735797",
  ice: "#96d9d6",
  dragon: "#6f35fc",
  dark: "#705746",
  steel: "#b7b7ce",
};
</script>
<template>
  <div class="item" @click="viewPokemonDetail">
    <div class="item__id">#{{ pokemon.id }}</div>
    <div
      class="item__image"
      :style="`background-image: url('https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/${pokemon.id}.png');`"
    ></div>
    <div class="item__name">{{ pokemon.name }}</div>
    <div class="item__type">
      <span
        v-for="type in pokemon.types"
        :key="type.type.name"
        class="type__name"
        :class="type.type.name"
        :style="{ backgroundColor: typeColors[type.type.name] }"
      >
        {{ type.type.name }}
      </span>
    </div>
  </div>
</template>
<style>
.item {
  display: flex;
  flex-direction: column;
  align-items: center;
}
.item__type {
  display: flex;
  flex-direction: row;
  justify-content: center;
  align-items: center;
  gap: 10px;
  font-size: 13px;
  text-transform: capitalize;
  margin-bottom: 5px;
  span {
    width: 100%;
    padding: 3px 10px;
    border-radius: 7px;
  }
}
</style>

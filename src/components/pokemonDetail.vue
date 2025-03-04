<script setup>
import { ref, onMounted } from "vue";
import { useRoute } from "vue-router";
import BackHome from "./BackHome.vue";
import pokemonEvo from "./pokemonEvo.vue";

const route = useRoute();
const pokemonDetail = ref({});
const pokemonSpecies = ref({});
const pokemonDesc = ref("");
const isLoading = ref(true);

async function fetchAPI(url) {
  const response = await fetch(url);
  return await response.json();
}

onMounted(() => {
  const name = route.params.name;

  isLoading.value = true;

  Promise.all([
    fetchAPI(`https://pokeapi.co/api/v2/pokemon/${name}`),
    fetchAPI(`https://pokeapi.co/api/v2/pokemon-species/${name}`),
  ]).then(([pokemonData, speciesData]) => {
    if (!pokemonData || !speciesData) {
      console.error("Failed to fetch Pokémon data.");
      isLoading.value = false;
      return;
    }

    pokemonDetail.value = pokemonData;
    pokemonSpecies.value = speciesData;

    pokemonDesc.value =
      speciesData.flavor_text_entries
        .find((entry) => entry.language.name === "en")
        ?.flavor_text.replace(/[\n\f]/g, " ") || "";

    setTimeout(() => {
      isLoading.value = false;
    }, 1000);
  });
});

// Định nghĩa màu sắc cho các loại Pokémon
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

const statsPara = {
  hp: "HP",
  attack: "ATK",
  defense: "DEF",
  "special-attack": "SPA",
  "special-defense": "SPD",
  speed: "SPD",
};

// Định nghĩa màu sắc chỉ số Pokémon
const colorLabel = {
  hp: "#df2140",
  attack: "#ff994d",
  defense: "#eecd3d",
  "special-attack": "#85ddff",
  "special-defense": "#96da83",
  speed: "#96da83",
};
</script>

<template>
  <BackHome />
  <div v-if="isLoading" class="loading-overlay">
    <p class="loading-text">Loading Pokémon details...</p>
  </div>
  <div class="container" v-else-if="pokemonDetail.id">
    <div
      class="item__image"
      :style="{
        backgroundImage: `url(
          'https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/${pokemonDetail.id}.png'
        )`,
      }"
    ></div>

    <div class="item__type">
      <span
        v-for="type in pokemonDetail.types"
        :key="type.type.name"
        class="type_item"
        :style="{ backgroundColor: typeColors[type.type.name] }"
      >
        {{ type.type.name }}
      </span>
    </div>

    <div class="item__name">
      {{ pokemonDetail.name }}
    </div>

    <p class="item__desc">{{ pokemonDesc }}</p>

    <div class="item__hei-wei">
      <div>
        <h3>Height</h3>
        <div class="number">{{ pokemonDetail.height }}</div>
      </div>
      <div>
        <h3>Weight</h3>
        <div class="number">{{ pokemonDetail.weight }}</div>
      </div>
    </div>
    <h3>Abilities</h3>
    <div class="item__abilities">
      <div v-for="ability in pokemonDetail.abilities" :key="ability.ability.name">
        <div class="abi">
          {{ ability.ability.name }}
        </div>
      </div>
    </div>
    <h3>Stats</h3>
    <div class="item__stats">
      <div v-for="stat in pokemonDetail.stats" :key="stat.stat.name" class="stats">
        <div class="label" :style="{ backgroundColor: colorLabel[stat.stat.name] }">
          {{ statsPara[stat.stat.name] }}
        </div>
        <div>{{ stat.base_stat }}</div>
      </div>
    </div>

    <h3>Evolution</h3>
    <pokemonEvo :speciesUrl="`https://pokeapi.co/api/v2/pokemon-species/${pokemonDetail.id}/`" />
  </div>
</template>

<style>
.loading-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;

  display: flex;
  justify-content: center;
  align-items: center;
}

.loading-text {
  color: black;
  font-size: 24px;
  font-weight: bold;
}
a {
  position: fixed;
  top: 0%;
  left: 0%;
  margin-top: 20px;
  margin-left: 20px;
}
.container {
  display: flex;
  flex-direction: column;
  align-items: center;
  max-width: 500px;
  width: 100%;
}

.item__image {
  width: 200px;
  height: 200px;
  background-size: cover;
  background-position: center;
  margin-bottom: 5px;
}
.item__type {
  display: flex;
  justify-content: center;
  gap: 10px;
  font-size: 13px;
  text-transform: capitalize;
  margin-bottom: 5px;
}
.type_item {
  padding: 3px 10px;
  border-radius: 7px;
}
.item__name {
  text-transform: capitalize;
  font-size: 22.5px;
  font-weight: 800;
  margin-bottom: 5px;
}
.item__desc {
  font-size: 15px;
  text-align: center;
  margin-bottom: 5px;
}
.item__hei-wei {
  margin-bottom: 5px;
  display: flex;
  justify-content: space-evenly;
  width: 100%;
  div {
    display: flex;
    flex-direction: column;
    align-items: center;
  }
}
.number,
.abi {
  width: 100px;
  padding: 2px;
  border-radius: 30px;
  background-color: #f6f8fc;
  text-align: center;
  text-transform: capitalize;
}
.item__abilities {
  display: flex;
  justify-content: space-evenly;
  margin-bottom: 5px;
  width: 100%;
}
.item__stats {
  display: flex;
  justify-content: space-evenly;
  margin-bottom: 5px;
  width: 100%;
}
.stats {
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 5px;
  background-color: #f6f8fc;
  border-radius: 30px;
  box-shadow: #63636333 0 2px 8px;
}
.label {
  display: flex;
  justify-content: center;
  align-items: center;
  border-radius: 50%;
  width: 25px;
  height: 25px;
  font-size: 10px;
  font-weight: 700;
}
</style>

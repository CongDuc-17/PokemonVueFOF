<script setup>
import { ref, onMounted } from "vue";

const props = defineProps(["speciesUrl"]);
const evolutionChain = ref([]);

async function fetchEvo() {
  if (!props.speciesUrl) return;
  const speciesData = await (await fetch(props.speciesUrl)).json();
  const evolutionData = await (await fetch(speciesData.evolution_chain.url)).json();

  let current = evolutionData.chain;
  while (current) {
    const id = current.species.url.split("/").slice(-2, -1)[0];
    evolutionChain.value.push({
      name: current.species.name,
      image: `https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/${id}.png`,
    });
    current = current.evolves_to[0] || null;
  }
}

onMounted(fetchEvo);
</script>

<template>
  <div v-if="evolutionChain.length > 1" class="evo">
    <div class="evolution">
      <div v-for="(evo, index) in evolutionChain" :key="evo.name" class="evo-stage">
        <p>{{ evo.name }}</p>
        <img :src="evo.image" :alt="evo.name" />
        <span v-if="index < evolutionChain.length - 1"> > </span>
      </div>
    </div>
  </div>
</template>

<style>
.evo {
  text-align: center;
}
.evo p {
  font-weight: 600;
  text-align: center;
  text-transform: capitalize;
}
.evolution {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 10px;
  margin: 0px;
}
.evo-stage {
  display: flex;
  align-items: center;
  gap: 5px;
}
.evo-stage img {
  width: 100px;
  height: 100px;
}
</style>

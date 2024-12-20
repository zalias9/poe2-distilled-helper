<script setup>
import { ref, reactive, watch } from 'vue'
import emotions from '../public/distilled_emotions.json'
import recipes from '../public/distilled_emotions_recipes.json'
import RecipeTableComponent from './components/RecipeTableComponent.vue'

const emotes = reactive(
  emotions.reduce((acc, emote) => {
    acc[emote.name] = Number("0");
    return acc
  }, {})
)

const search = ref('')

const resetEmotes = (event) => {
  for (const emote in emotes) {
    emotes[emote] = 0
  }
  // Reset all number inputs to zero
  const inputs = document.querySelectorAll('input[type="number"]');
  inputs.forEach(input => {
    input.value = 0;
  });
  searchResults = []
}

const findCraftableItems = (availableOils, possibleOutcomes) => {
  const availableCounter = new Map();
  for (const [oil, count] of Object.entries(availableOils)) {
    availableCounter.set(oil, count);
  }

  const craftableItems = [];

  for (const outcome of possibleOutcomes) {
    const requiredOils = new Map();
    for (const oil of outcome.distilled_emotions) {
      requiredOils.set(oil, (requiredOils.get(oil) || 0) + 1);
    }

    if (Array.from(requiredOils.entries()).every(([oil, count]) =>
      (availableCounter.get(oil) || 0) >= count)) {
      craftableItems.push(outcome);
    }
  }

  return craftableItems;
}

let craftable = []
let searchResults = []

watch(emotes, () => {
  craftable = findCraftableItems(emotes, recipes);
})

watch(search, (newSearch) => {
  const string = newSearch.trim().toLowerCase();
  if (string.length > 0) {
    if (craftable.length > 0) {
      searchResults = craftable.filter(item =>
        item.passive_name.toLowerCase().includes(string) ||
        item.passive_desc.some(line => line.toLowerCase().includes(string))
      )
    } else {
      searchResults = recipes.filter(item =>
        item.passive_name.toLowerCase().includes(string) ||
        item.passive_desc.some(line => line.toLowerCase().includes(string))
      )
    }
  } else {
    searchResults = []
  }
})

</script>

<style>
@import "./public/css/pico.min.css";

table {
  white-space: nowrap;
}
</style>

<template>
  <hr />
  <div class="container-fluid">
    <h1>Distilled Emotions Helper</h1>
    <div :class="`grid`">
      <div v-for="count, name in emotes">
        <label>
          <img :src="`/distilled_images/${name}.webp`" width="32" height="32" />
          <text>{{ name.split(" ").slice(1)[0] }}</text>
          <input type="number" value="0" min="0" max="3" @input="emotes[name] = Number($event.target.value); search = ''">
        </label>
      </div>
    </div>
    <div>
    </div>
    <hr />
    <!-- Search Bar -->
    <div style="display: flex; gap: 10px;">
      <input type="search" placeholder="Search" v-model="search" />
      <button @click="search = ``">Clear Search</button>
      <button @click="resetEmotes" :class="`secondary`">Reset Emotions</button>
    </div>
    <p>Number of craftable items: {{ craftable.length }}</p>
    <!-- Recipes Table -->
    <div>
      <!-- All emotes are zero -->
      <div v-if="!Object.values(emotes).some(x => x > 0) && !search">
        <RecipeTableComponent :recipes="recipes" />
      </div>
      <div v-else-if="search">
        <RecipeTableComponent :recipes="searchResults" />
      </div>
      <div v-else>
        <RecipeTableComponent :recipes="craftable" />
      </div>
    </div>
  </div>
</template>

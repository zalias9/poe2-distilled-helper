<template>
  <table class="striped">
    <tbody>
      <tr>
        <th>
          <h5>Distilled Emotions</h5>
        </th>
        <th>
          <h5>Passive Name</h5>
        </th>
        <th>
          <h5>Passive Description</h5>
        </th>
      </tr>
      <tr v-for="item in recipes">
        <td>
          <ul>
            <li v-for="emote in item.distilled_emotions">
              <img :src="`/distilled_images/${emote}.webp`" width="32" height="32" />
              <text>{{ emote }}</text>
            </li>
          </ul>
        </td>
        <td>{{ item.passive_name }}</td>
        <td>
          <ul>
            <li v-for="line in item.passive_desc">
              <text>{{ line }}</text>
            </li>
          </ul>
        </td>
      </tr>
    </tbody>
  </table>
</template>

<script setup>
import { defineProps } from 'vue'
const props = defineProps({
  recipes: Array
})
</script>
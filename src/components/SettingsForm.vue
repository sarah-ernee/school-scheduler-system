<template>
  <v-card class="p-4 bg-white rounded-lg shadow-md w-full max-w-xs">
    <div class="text-gray-700 font-semibold mb-2">Visiting Duration</div>
    <v-select
      v-model="selectDuration"
      variant="outlined"
      label="Select"
      :items="durationOptions"
      width="100px"
    ></v-select>

    <div class="text-gray-700 font-semibold mb-2">No. of Booking Sessions</div>
    <v-text-field
      v-model="bookingSessions"
      variant="outlined"
      type="number"
    ></v-text-field>

    <v-checkbox v-model="toggleTour">
      <template #label>
        <div class="text-gray-700 font-semibold mb-2">
          No. of Booking Sessions
        </div>
      </template>
    </v-checkbox>
  </v-card>
</template>

<script setup lang="ts">
import { ref } from 'vue'

const selectDuration = ref<string>('')
const bookingSessions = ref<number>(0)
const toggleTour = ref<boolean>(false)

const durationOptions = [
  '15 min',
  '30 min',
  '45 min',
  '60 min',
  '75 min',
  '90 min',
]
</script>

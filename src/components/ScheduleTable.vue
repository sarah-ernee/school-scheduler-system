<template>
  <v-card class="mt-4 p-4 rounded-lg shadow-md">
    <h2 class="text-gray-700 font-semibold mb-4">Schedule</h2>
    <table class="min-w-full">
      <thead>
        <tr>
          <th class="text-left text-gray-600">Day</th>
          <th class="text-left text-gray-600">Time Slots</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(slot, index) in scheduleSlots" :key="index">
          <td class="border-b p-2">{{ slot.day }}</td>
          <td class="border-b p-2">
            <div v-if="slot.isChecked">
              <div v-for="(timeSlot, timeIndex) in slot.timeSlots" :key="timeIndex">
                {{ formatTimeSlot(timeSlot) }}
              </div>
            </div>
            <div v-else class="text-gray-500">Unavailable</div>
          </td>
        </tr>
      </tbody>
    </table>
  </v-card>
</template>

<script setup lang="ts">
import { defineProps } from 'vue'

interface Time {
  startTime: string
  endTime: string
}

interface Slot {
  day: string
  timeSlots: Time[]
  isChecked: boolean
}

const props = defineProps<{
  scheduleSlots: Slot[]
}>()

const formatTimeSlot = (timeSlot: Time): string => {
  return `${timeSlot.startTime} - ${timeSlot.endTime}`
}
</script>

<style scoped>
table {
  width: 100%;
  border-collapse: collapse;
}

th,
td {
  padding: 0.5rem;
  text-align: left;
}

th {
  border-bottom: 2px solid #e0e0e0;
}

td {
  border-bottom: 1px solid #e0e0e0;
}
</style>

<template>
  <div class="absolute top-0 left-0 p-4">
    <v-card class="p-6 bg-white rounded-lg shadow-md w-full min-w-[400px]">
      <h1 class="text-gray-700 font-semibold mb-4">Visiting Duration</h1>
      <v-select
        v-model="selectDuration"
        variant="outlined"
        density="compact"
        label="Select"
        :items="durationOptions"
      />

      <h2 class="text-gray-700 font-semibold mb-4">No. of Booking Sessions</h2>
      <v-text-field
        v-model="bookingSessions"
        variant="outlined"
        density="compact"
        type="number"
      />

      <v-checkbox v-model="toggleTour" hide-details>
        <template #label>Allow video tour call</template>
      </v-checkbox>

      <v-card class="p-6 rounded-lg border-stone-500" variant="outlined">
        <div class="text-gray-700 font-semibold mb-1">Availability</div>
        <div class="text-sm text-gray-700 mb-4">
          Select your weekly recurring schedule
        </div>

        <div
          v-for="(item, index) in scheduleSlots"
          :key="index"
          class="flex items-start mb-4"
        >
          <div class="flex items-center gap-2 min-w-[100px]">
            <v-checkbox
              v-model="item.isChecked"
              hide-details
              @change="handleCheckChange(index)"
              class="mr-2"
            />
            <span class="text-sm text-gray-700 font-semibold">{{
              item.day
            }}</span>
          </div>

          <div v-if="item.isChecked" class="ml-8">
            <div
              v-for="(timeSlot, slotIndex) in item.timeSlots"
              :key="slotIndex"
              class="flex items-center gap-2 mb-2"
            >
              <v-text-field
                v-model="timeSlot.startTime"
                variant="outlined"
                density="compact"
                type="time"
                label="Start Time"
                hide-details
                @change="validateTimeSlot(index, slotIndex)"
                class="w-[100px]"
              />
              <v-text-field
                v-model="timeSlot.endTime"
                variant="outlined"
                density="compact"
                type="time"
                label="End Time"
                hide-details
                @change="validateTimeSlot(index, slotIndex)"
                class="w-[100px]"
              />
              <v-btn
                variant="outlined"
                density="compact"
                @click="deleteTimeSlot(index, slotIndex)"
                icon
                color="blue"
                size="small"
                >-</v-btn
              >
              <v-btn
                variant="outlined"
                density="compact"
                @click="addTimeSlot(index)"
                icon
                color="blue"
                size="small"
                >+</v-btn
              >
            </div>
          </div>

          <div v-else class="text-sm text-gray-700 my-auto">Unavailable</div>
        </div>
      </v-card>
    </v-card>

    <!-- Commented out due to dependency issue so the rest of the code still loads -->
    <!-- <ScheduleTable :scheduleSlots="scheduleSlots" /> -->
  </div>
</template>

<script setup lang="ts">
import { ref, watch } from 'vue'
// @ts-ignore
import ScheduleTable from './ScheduleTable.vue'

interface Time {
  startTime: string
  endTime: string
}

interface Slot {
  day: string
  timeSlots: Time[]
  isChecked: boolean
}

const selectDuration = ref<string>('')
const bookingSessions = ref<number>(0)
const toggleTour = ref<boolean>(false)

const scheduleSlots = ref<Slot[]>([
  {
    day: 'Mon',
    timeSlots: [{ startTime: '09:00', endTime: '10:00' }],
    isChecked: false,
  },
  {
    day: 'Tues',
    timeSlots: [{ startTime: '09:00', endTime: '10:00' }],
    isChecked: false,
  },
  {
    day: 'Wed',
    timeSlots: [{ startTime: '09:00', endTime: '10:00' }],
    isChecked: false,
  },
  {
    day: 'Thurs',
    timeSlots: [{ startTime: '09:00', endTime: '10:00' }],
    isChecked: false,
  },
  {
    day: 'Fri',
    timeSlots: [{ startTime: '09:00', endTime: '10:00' }],
    isChecked: false,
  },
  {
    day: 'Sat',
    timeSlots: [{ startTime: '09:00', endTime: '10:00' }],
    isChecked: false,
  },
  {
    day: 'Sun',
    timeSlots: [{ startTime: '09:00', endTime: '10:00' }],
    isChecked: false,
  },
])

const durationOptions = [
  '15 min',
  '30 min',
  '45 min',
  '60 min',
  '75 min',
  '90 min',
]

watch(selectDuration, newDuration => {
  const durationInMinutes = parseInt(newDuration.split(' ')[0])
  updateTimeSlots(durationInMinutes)
})

const updateTimeSlots = (duration: number): void => {
  scheduleSlots.value.forEach(slot => {
    if (slot.isChecked) {
      for (let i = 0; i < bookingSessions.value; i++) {
        const startTime = new Date(
          `1970-01-01T${slot.timeSlots[0].startTime}:00`,
        )
        startTime.setMinutes(startTime.getMinutes() + i * duration)
        const endTime = new Date(startTime)
        endTime.setMinutes(endTime.getMinutes() + duration)

        if (i < slot.timeSlots.length) {
          slot.timeSlots[i].startTime = startTime.toTimeString().slice(0, 5)
          slot.timeSlots[i].endTime = endTime.toTimeString().slice(0, 5)
        } else {
          slot.timeSlots.push({
            startTime: startTime.toTimeString().slice(0, 5),
            endTime: endTime.toTimeString().slice(0, 5),
          })
        }
      }
      while (slot.timeSlots.length > bookingSessions.value) {
        slot.timeSlots.pop()
      }
    }
  })
}

const deleteTimeSlot = (index: number, slotIndex: number): void => {
  scheduleSlots.value[index].timeSlots.splice(slotIndex, 1)
  if (scheduleSlots.value[index].timeSlots.length === 0) {
    scheduleSlots.value[index].isChecked = false
  }
}

const addTimeSlot = (index: number): void => {
  if (scheduleSlots.value[index].timeSlots.length < bookingSessions.value) {
    const lastSlot =
      scheduleSlots.value[index].timeSlots[
        scheduleSlots.value[index].timeSlots.length - 1
      ]
    const newStartTime = new Date(`1970-01-01T${lastSlot.endTime}:00`)
    const durationInMinutes = parseInt(selectDuration.value.split(' ')[0])

    newStartTime.setMinutes(newStartTime.getMinutes() + durationInMinutes)
    const newEndTime = new Date(newStartTime)
    newEndTime.setMinutes(newEndTime.getMinutes() + durationInMinutes)

    scheduleSlots.value[index].timeSlots.push({
      startTime: newStartTime.toTimeString().slice(0, 5),
      endTime: newEndTime.toTimeString().slice(0, 5),
    })
  } else {
    alert(`You can only add up to ${bookingSessions.value} booking sessions.`)
  }
}

const handleCheckChange = (index: number): void => {
  if (scheduleSlots.value[index].isChecked) {
    addTimeSlot(index)
  } else {
    scheduleSlots.value[index].timeSlots = []
  }
}

const isTimeValid = (startTime: string, endTime: string): boolean => {
  const start = new Date(`1970-01-01T${startTime}:00`)
  const end = new Date(`1970-01-01T${endTime}:00`)
  return start < end
}

const validateTimeSlot = (index: number, timeIndex: number) => {
  const timeSlot = scheduleSlots.value[index].timeSlots[timeIndex]
  if (!isTimeValid(timeSlot.startTime, timeSlot.endTime)) {
    alert('Invalid time slot. End time must be later than start time.')
  }
}
</script>

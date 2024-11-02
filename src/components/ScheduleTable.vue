<template>
  <FullCalendar
    :events="events"
    :options="calendarOptions"
    @dateClick="handleDateClick"
  />
</template>

<script setup lang="ts">
import { defineProps, computed } from 'vue'
import FullCalendar from 'vue-full-calendar'
import '@fullcalendar/main.min.css'

interface TimeSlot {
  startTime: string
  endTime: string
}

interface Slot {
  day: string
  timeSlots: TimeSlot[]
  isChecked: boolean
}

const props = defineProps<{
  scheduleSlots: Slot[]
}>()

const daysMap: { [key: string]: number } = {
  Mon: 1,
  Tues: 2,
  Wed: 3,
  Thurs: 4,
  Fri: 5,
  Sat: 6,
  Sun: 0,
}

const events = computed(() => {
  const allEvents: any[] = []
  props.scheduleSlots.forEach(slot => {
    slot.timeSlots.forEach(timeSlot => {
      const event = {
        title: 'Available',
        start: `2024-11-01T${timeSlot.startTime}:00`,
        end: `2024-11-01T${timeSlot.endTime}:00`,
        dow: [daysMap[slot.day]],
      }
      allEvents.push(event)
    })
  })
  return allEvents
})

const calendarOptions = {
  initialView: 'timeGridWeek',
  headerToolbar: {
    left: 'prev,next today',
    center: 'title',
    right: 'timeGridWeek,timeGridDay',
  },
  slotDuration: '00:30:00',
  selectable: true,
  editable: true,
  events: events.value,
}

const handleDateClick = (arg: any) => {
  console.log('Date clicked: ', arg.date)
}
</script>

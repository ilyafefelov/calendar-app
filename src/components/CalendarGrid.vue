<template>
  <v-container fluid>
    <v-row>
      <v-col
        v-for="day in daysOfWeek"
        :key="day"
        class="calendar-header"
        :cols="colWidth"
      >
        {{ day }}
      </v-col>
    </v-row>
    <v-row
      v-for="(week, weekIndex) in calendarRows"
      :key="weekIndex"
      :style="{ height: getRowHeight(week) + 'px' }"
    >
      <v-col
        v-for="(cell, cellIndex) in week"
        :key="cellIndex"
        class="calendar-cell"
        :cols="colWidth"
      >
        <div class="calendar-date">{{ cell.date }}</div>
        <div class="flex flex-col mt-5">
          <EventCard
            v-for="(event, i) in cell.events"
            :key="event.name + i"
            class="event"
            :event="event"
            :categories="categories"
          />
        </div>
      </v-col>
    </v-row>
  </v-container>
</template>

<script setup lang="ts">
import { ref, watch } from "vue";
import EventCard from "@/components/EventCard.vue";

interface Event {
    name: string;
    categoryId: string;
    startDate: string;
    endDate: string;
}

interface Category {
    name: string;
    id: string;
}

const props = defineProps<{
    events: Event[];
    categories: Category[];
    month: number;
    year: number;
}>();

const daysOfWeek = ["Пн","Вт", "Ср", "Чт", "Пт", "Сб", "Нд"];
const daysInMonth = (month: number, year: number) => {
    return new Date(year, month + 1, 0).getDate();
};
const firstDayOfMonth = (month: number, year: number) => {
    return new Date(year, month, 0).getDay();
};
const colWidth = 12 / daysOfWeek.length;

const generateCalendarCells = (
    month: number,
    year: number,
    events: Event[]
) => {
    const totalDays = daysInMonth(month, year);
    const firstDay = firstDayOfMonth(month, year);
    const calendarCells: { date: string; events: Event[] }[][] = [];
    let date = 1;

    for (let i = 0; i < 6; i++) {
        const week: { date: string; events: Event[] }[] = [];
        for (let j = 0; j < 7; j++) {
            if (i === 0 && j < firstDay) {
                week.push({ date: "", events: [] });
            } else if (date > totalDays) {
                week.push({ date: "", events: [] });
            } else {
                const cellEvents = events.filter((event) => {
                    const eventDate = new Date(event.startDate);
                    return (
                        eventDate.getFullYear() === year &&
                        eventDate.getMonth() === month &&
                        eventDate.getDate() === date
                    );
                });
                week.push({ date: date.toString(), events: cellEvents });
                date++;
            }
        }
        calendarCells.push(week);
    }

    while (
        calendarCells.length > 0 &&
        calendarCells[calendarCells.length - 1].every((cell) => !cell.date)
    ) {
        calendarCells.pop();
    }

    return calendarCells;
};

const calendarRows = ref(
    generateCalendarCells(props.month, props.year, props.events)
);

watch(
    [() => props.month, () => props.year, () => props.events],
    () => {
        calendarRows.value = generateCalendarCells(
            props.month,
            props.year,
            props.events
        );
    },
    { immediate: true }
);

// Formatting the height of the row
const baseRowHeight = 100;
const eventHeight = 20;

const getRowHeight = (week: { date: string; events: Event[] }[]) => {
    const maxEventsInDay = Math.max(...week.map((cell) => cell.events.length));
    return baseRowHeight + maxEventsInDay * eventHeight;
};
</script>

<style scoped>
.calendar-header {
    @apply text-center font-bold select-none;
}

.calendar-cell {
    @apply border border-gray-200 overflow-hidden relative w-full;
}

.calendar-date {
    @apply absolute top-0 right-0 p-1 select-none;
}

.event {
    @apply mx-0 my-0.5 p-[2px] rounded-md text-xs;
}
</style>

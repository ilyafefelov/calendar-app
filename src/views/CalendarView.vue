<template>
  <v-container fluid class="p-4">
    <CalendarHeader
      :categories="categories"
      @month-change="handleMonthChange"
      @year-change="handleYearChange"
      @category-change="handleCategoryChange"
    />
    <CalendarGrid
      :categories="categories"
      :events="filteredEvents"
      :month="selectedMonth"
      :year="selectedYear"
    />
    <CreateEventModal @eventCreated="fetchEvents" :categories="categories" />
    <CalendarFooter
      :content="footerContent"
      @update:content="updateFooterContent"
    />
  </v-container>
</template>

<script setup lang="ts">
import { ref, onMounted } from "vue";
import CalendarHeader from "@/components/CalendarHeader.vue";
import CalendarGrid from "@/components/CalendarGrid.vue";
import CalendarFooter from "@/components/CalendarFooter.vue";
import CreateEventModal from "@/components/CreateEventModal.vue";

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

const events = ref<Event[]>([]);
const filteredEvents = ref<Event[]>([]);
const categories = ref<Category[]>([
  { name: "Конференція", id: "4d115ae7-ef38-45b7-baf3-8fd9e080a596" },
  { name: "Свято", id: "d6ef1e8d-c49f-4231-91cb-c3363a943fd8" },
  { name: "Навчання", id: "23585758-9bdc-4d54-8b98-670c6f1dda5f" },
]);

const selectedMonth = ref(new Date().getMonth());
const selectedYear = ref(new Date().getFullYear());
const selectedCategories = ref<string[]>([]);

const fetchEvents = async () => {
    try {
        const response = await fetch(import.meta.env.VITE_API_URL + '/Event/All', {
            headers: {
                Authorization: `Bearer ` + import.meta.env.VITE_API_TOKEN
            }
        });
        if (!response.ok) {
            throw new Error('Network response was not ok');
        }
        events.value = await response.json();
        filteredEvents.value = events.value;
    } catch (error) {
        console.error('Error fetching events:', error);
    }
};

const handleMonthChange = (month: number) => {
  selectedMonth.value = month;
};

const handleYearChange = (year: number) => {
  selectedYear.value = year;
};

const handleCategoryChange = (categories: string[]) => {
  selectedCategories.value = categories;
  filteredEvents.value = events.value.filter((event) => {
    return (
      selectedCategories.value.length === 0 ||
      selectedCategories.value.includes(event.categoryId)
    );
  });
};

// Change this to test an svg!!
// const footerContent = ref<string>(
//   '<img style="display: block;-webkit-user-select: none;margin: auto;cursor: zoom-in;background-color: hsl(0, 0%, 90%);transition: background-color 300ms;" src="https://upload.wikimedia.org/wikipedia/commons/thumb/4/4f/SVG_Logo.svg/1200px-SVG_Logo.svg.png" width="279" height="279">'
// );
const footerContent = ref<string>('');

const updateFooterContent = (newContent: string) => {
  footerContent.value = newContent;
};

onMounted(() => {
  fetchEvents();
  console.log("Calendar view mounted");
  const savedContent = localStorage.getItem("footerContent");
    if (savedContent) {
        footerContent.value = savedContent;
    }
});
</script>

<template>
  <v-row>
    <v-col cols="4">
      <v-select
        v-model="selectedMonth"
        :items="months"
        item-title="text"
        item-value="value"
        label="Виберіть Місяць"
        @update:modelValue="emitMonthChange"
		flat
		bg-color="white"
      ></v-select>
    </v-col>
    <v-col cols="4">
      <v-select
        v-model="selectedYear"
        :items="years"
        label="Виберіть Рік"
		flat
		bg-color="white"
        @update:modelValue="emitYearChange"
      ></v-select>
    </v-col>
    <v-col cols="4">
      <v-select
        v-model="selectedCategory"
        :items="categoryNames"
        label="Виберіть категорію"
        multiple
        chips
		closable-chips
		flat
		bg-color="white"
        @update:modelValue="emitCategoryChange"
      ></v-select>
    </v-col>
  </v-row>
</template>

<script setup lang="ts">
import { ref, defineProps, defineEmits, computed } from "vue";

interface Category {
    name: string;
    id: string;
}

const props = defineProps<{
    categories: Category[];
}>();

const emit = defineEmits<{
    (e: "month-change", month: number): void;
    (e: "year-change", year: number): void;
    (e: "category-change", categories: string[]): void;
}>();

const selectedMonth = ref(new Date().getMonth());
const selectedYear = ref(new Date().getFullYear());

const categoryNames = computed(() =>
    props.categories.map((category) => category.name)
);

const selectedCategory = ref<string[]>([]);

const months = [
    { text: "Січень", value: 0 },
    { text: "Лютий", value: 1 },
    { text: "Березень", value: 2 },
    { text: "Квітень", value: 3 },
    { text: "Травень", value: 4 },
    { text: "Червень", value: 5 },
    { text: "Липень", value: 6 },
    { text: "Серпень", value: 7 },
    { text: "Вересень", value: 8 },
    { text: "Жовтень", value: 9 },
    { text: "Листопад", value: 10 },
    { text: "Грудень", value: 11 },
];
const years = ref([2022, 2023, 2024, 2025, 2026, 2027]);

const emitMonthChange = () => {
    console.log(selectedMonth.value);
    emit("month-change", selectedMonth.value);
};

const emitYearChange = () => {
    emit("year-change", selectedYear.value);
};

const emitCategoryChange = () => {
    const selectedCategoryIds = selectedCategory.value.map((categoryName) => {
        const category = props.categories.find((c) => c.name === categoryName);
        return category ? category.id : "";
    });
    emit("category-change", selectedCategoryIds);
};
</script>

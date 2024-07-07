<template>
    <v-chip size="x-small bg-transperant" width="100%" :color="chipColor" label>
        <v-icon class="ml-1" :icon="chipIcon" start></v-icon>
        <div class="font-sm whitespace-nowrap overflow-hidden text-ellipsis max-w-[calc((100vw-36em)/7-1em)]"
            :class="chipClass">
            {{ event.name }}
        </div>
    </v-chip>
</template>

<script setup lang="ts">
import { defineProps, computed } from "vue";

interface Event {
    name: string;
    categoryId: string;
    startDate: string;
    endDate: string;
}

const props = defineProps<{
    event: Event;
    categories: { name: string; id: string }[];
}>();

const chipColor = computed(() => {
    switch (props.event.categoryId) {
        case "4d115ae7-ef38-45b7-baf3-8fd9e080a596": // Конференція
            return "blue";
        case "d6ef1e8d-c49f-4231-91cb-c3363a943fd8": // Свято
            return "pink";
        case "23585758-9bdc-4d54-8b98-670c6f1dda5f": // Навчання
            return "green";
        default:
            return "primary";
    }
});

const chipIcon = computed(() => {
    switch (props.event.categoryId) {
        case "4d115ae7-ef38-45b7-baf3-8fd9e080a596": // Конференція
            return "mdi-account-tie";
        case "d6ef1e8d-c49f-4231-91cb-c3363a943fd8": // Свято
            return "mdi-party-popper";
        case "23585758-9bdc-4d54-8b98-670c6f1dda5f": // Навчання
            return "mdi-school";
        default:
            return "mdi-calendar";
    }
});

const chipClass = computed(() => {
    return {
        "conference-chip-text":
            props.event.categoryId === "4d115ae7-ef38-45b7-baf3-8fd9e080a596",
        "holiday-chip-text":
            props.event.categoryId === "d6ef1e8d-c49f-4231-91cb-c3363a943fd8",
        "education-chip-text":
            props.event.categoryId === "23585758-9bdc-4d54-8b98-670c6f1dda5f",
    };
});
</script>

<style scoped>
.conference-chip-text {
    font-weight: bold;
}

.holiday-chip-text {
    color: rgb(171, 4, 135);
    font: italic bold 10px/12px Georgia, serif;
}

.education-chip-text {
    font-family: "Courier New", Courier, monospace;
    font-weight: bold;
    letter-spacing: -0.3px;
}
</style>

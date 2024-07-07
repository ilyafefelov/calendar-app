<template>
    <v-dialog v-model="dialog" max-width="800" class="create-event-modal">
        <template v-slot:activator="{ props: activatorProps }">
            <v-btn prepend-icon="mdi-plus" v-bind="activatorProps" color="surface-variant"
                class="fixed bottom-8 right-8 z-50" text-variant="flat">
                подія
            </v-btn>
        </template>
        <template v-slot:default>
            <v-card title="Створити Подію" class="p-8">
                <v-form ref="form" fast-fail @submit.prevent="handleSubmit">
                    <v-text-field type="text" label="Назва Події"
                        v-model="eventName" :rules="[rules.required]"
                        :error-messages="submitAttempted ? eventNameErrors : []"
                        required />
                    <v-select v-model="selectedCategory" label="Категорія"
                        :items="categories" item-title="name" item-value="id"
                        :rules="[rules.required]"
                        :error-messages="submitAttempted ? categoryErrors : []"
                        required />
                    <v-date-input v-model="startDate" label="Дата початку"
                        :rules="[rules.required]"
                        :error-messages="submitAttempted ? startDateErrors : []"
                        no-title required />
                    <v-date-input v-model="endDate" label="Дата завершення"
                        :rules="[rules.required, rules.dateAfterStart]"
                        :error-messages="submitAttempted ? endDateErrors : []"
                        no-title required />
                    <v-card-actions>
                        <v-spacer></v-spacer>
                        <v-btn type="submit">Створити</v-btn>
                    </v-card-actions>
                </v-form>
            </v-card>
        </template>
    </v-dialog>
</template>

<script setup lang="ts">
import { ref, computed } from 'vue';


interface Event {
    name: string;
    categoryId: string;
    startDate: string;
    endDate: string;
}

defineProps<{ categories: any[] }>();
const emit = defineEmits(['eventCreated']);

const dialog = ref(false);
const submitAttempted = ref(false);
const eventName = ref('');
const selectedCategory = ref('');
const startDate = ref<string | null>(null);
const endDate = ref<string | null>(null);

const rules = {
    required: (value: string) => !!value || 'Треба заповнити це поле.',
    dateAfterStart: (value: string) => {
        if (!startDate.value) return true;
        return new Date(value) >= new Date(startDate.value) || 'Остання дата має бути пізніше за початкову.';
    }
};

const eventNameErrors = computed(() => {
    return !eventName.value ? 'Назва події необхідна' : '';
});

const categoryErrors = computed(() => {
    return !selectedCategory.value ? 'Категорія необхідна' : '';
});

const startDateErrors = computed(() => {
    return !startDate.value ? 'Дата початку необхідна' : '';
});

const endDateErrors = computed(() => {
    if (!endDate.value) return 'Дата завершення необхідна';
    if (startDate.value && new Date(endDate.value) < new Date(startDate.value)) {
        return 'Остання дата має бути пізніше за початкову.';
    }
    return '';
});

const handleSubmit = () => {
    submitAttempted.value = true;

    // Check for errors before proceeding
    if (eventNameErrors.value || categoryErrors.value || startDateErrors.value || endDateErrors.value) {
        return;
    }

    createEvent();
};

const createEvent = async () => {
    if (!startDate.value || !endDate.value) {
        console.error('Start date or end date is null');
        return;
    }

    const event: Event = {
        name: eventName.value,
        categoryId: selectedCategory.value,
        startDate: startDate.value,
        endDate: endDate.value,
    };

    try {
        const response = await fetch(import.meta.env.VITE_API_URL + '/Event/Create', {
            method: 'POST',
            headers: {
                'Content-Type': 'application/json',
                Authorization: `Bearer ` + import.meta.env.VITE_API_TOKEN
            },
            body: JSON.stringify(event)
        });

        if (!response.ok) {
            throw new Error('Network response was not ok');
        }

        const data = await response.json();
        console.log('Event created:', data);
        emit('eventCreated', data);
        
        dialog.value = false;
        resetForm();
        submitAttempted.value = false;
    } catch (error) {
        console.error('Error creating event:', error);
    }
};

const resetForm = () => {
    eventName.value = '';
    selectedCategory.value = '';
    startDate.value = null;
    endDate.value = null;
};
</script>



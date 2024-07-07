<template>
    <v-footer>
        <v-container fluid class="mt-8">
            <v-btn variant="outlined" title="Edit Footer"
            :class="[
                'hover:text-blue-900 text-blue-200 absolute top-0 transition-transform mt-2 backdrop-blur-sm bg-white/10 hover:bg-blue-200',
                 {'rounded-full pl-6':!isEditing},
                 ]"
                  prepend-icon="mdi-pencil" @click="toggleEdit">
                {{ isEditing ? 'Save' : '' }}
            </v-btn>
            <div v-if="!isEditing" v-html="content"></div>
            <div v-else >
                <v-textarea v-model="editableContent"
                    label="Footer Content"></v-textarea>
            </div>
        </v-container>
    </v-footer>
</template>

<script setup lang="ts">
import { ref, defineProps, defineEmits, watch, onMounted } from 'vue';

const props = defineProps<{
    content: string;
}>();
const emit = defineEmits(['update:content']);

const isEditing = ref(false);
const editableContent = ref(props.content);

const toggleEdit = () => {
   try {
     if (isEditing.value) {
         // Save the content to localStorage
         localStorage.setItem('footerContent', editableContent.value);
         emit('update:content', editableContent.value);
     }
     isEditing.value = !isEditing.value;
   } catch (error) {
        console.error("Error during footer edit: ", error);
   }
};

onMounted(() => {
    const savedContent = localStorage.getItem('footerContent');
    if (savedContent) {
        editableContent.value = savedContent;
        emit('update:content', savedContent);
    }
});

watch(() => props.content, (newContent) => {
    editableContent.value = newContent;
});
</script>
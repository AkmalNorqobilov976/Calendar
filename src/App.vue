<script setup lang="ts">
import { ref } from 'vue';
import Calendar from './components/CalendarComponent.vue'
const oneSelect = ref('');
const multiSelect = ref([]);
</script>

<template>
    <div>
        {{ oneSelect }}
        <Calendar @get-value="oneSelect = $event"/>
        <p class="mt-5"> MultiSelect  Calendar</p>
        {{ multiSelect }}
        <Calendar :is-multi="true" @get-value="multiSelect = $event"/>
    </div>
</template>

<style scoped>
.mt-5 {
    margin-top: 5rem;
}
.logo {
    height: 6em;
    padding: 1.5em;
    will-change: filter;
    transition: filter 300ms;
}

.logo:hover {
    filter: drop-shadow(0 0 2em #646cffaa);
}

.logo.vue:hover {
    filter: drop-shadow(0 0 2em #42b883aa);
}
</style>

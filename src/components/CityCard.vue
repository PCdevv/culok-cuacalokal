<template>
    <div
        class="flex py-6 px-3 bg-weather-secondary rounded-md shadow-md cursor-pointer"
    >
        <div class="flex-flex-col flex-1">
            <h2 class="text-3xl">{{ state.city }}</h2>
            <h3 class="text-3xl">{{ state.state }}</h3>
        </div>
        <div class="flex-flex-col gap-2">
            <p class="text-3xl self-end">
                {{ Math.round(state.weather.main.temp) }}&deg;
            </p>
            <div class="flex gap-2">
                <span class="text-xs">
                    H: {{ Math.round(state.weather.main.temp_max) }}&deg;
                </span>
                <span class="text-xs">
                    L: {{ Math.round(state.weather.main.temp_min) }}&deg;
                </span>
            </div>
        </div>
    </div>
</template>

<script setup>
defineProps({
    state: {
        type: Object,
        defaut: () => {},
    },
});
</script>

<style lang="scss" scoped></style>

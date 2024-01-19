<template>
    <div class="flex flex-col flex-1">
        <!-- Weather Overview -->
        <div class="flex flex-col items-center py-12">
            <AnimatedPlaceholder class="max-w-[300px] w-full mb-2" />
            <AnimatedPlaceholder class="max-w-[300px] w-full mb-2" />
            <AnimatedPlaceholder class="max-w-[300px] h-[100px] w-full mb-2" />
            <AnimatedPlaceholder class="max-w-[300px] w-full mb-8" />
            <AnimatedPlaceholder class="max-w-[300px] h-[75] w-full" />
        </div>
        <!-- Today Weather -->
        <div class="flex flex-col items-center py-12">
            <AnimatedPlaceholder
                class="max-w-screen-md h-[100px] w-full mb-12"
            />
        </div>
    </div>
</template>

<script setup>
import AnimatedPlaceholder from "./AnimatedPlaceholder.vue";
</script>

<style lang="scss" scoped></style>

<template>
    <div>
        <Suspense>
            <template #default>
                <AsyncCityView />
            </template>
            <template #fallback>
                <!-- <p>Loading</p> -->

                <CityViewSkeleton />
            </template>
        </Suspense>
    </div>
</template>

<script setup>
import CityViewSkeleton from "../components/CityViewSkeleton.vue";
import AsyncCityView from "../components/AsyncCityView.vue";
import { Suspense } from "vue";
</script>

<style lang="scss" scoped></style>

<template>
    <div v-for="state in savedCities" :key="state.id">
        <CityCard :state="state" @click="goToStateView(state)" />
    </div>
    <p v-if="savedCities.length === 0">
        No location added. To start tracking a locaton, search in the field
        above.
    </p>
</template>

<script setup>
import axios from "axios";
import CityCard from "./CityCard.vue";
import { useRouter } from "vue-router";
import { ref } from "vue";

const savedCities = ref([]);
const getStates = async () => {
    if (localStorage.getItem("savedCities")) {
        savedCities.value = JSON.parse(localStorage.getItem("savedCities"));

        const requests = [];
        savedCities.value.forEach((state) => {
            requests.push(
                axios.get(
                    `${import.meta.env.VITE_BASE_URL}data/2.5/weather?lat=${
                        state.coords.lat
                    }&lon=${state.coords.lon}&appid=${
                        import.meta.env.VITE_API_KEY
                    }&units=metric`
                )
            );
        });
        const weatherData = await Promise.all(requests);

        // Flicker Delay
        // await new PromiseRejectionEvent((res) => setTimeout(res, 1000));

        weatherData.forEach((value, index) => {
            savedCities.value[index].weather = value.data;
        });
    }
};

await getStates();

const router = useRouter();
const goToStateView = (state) => {
    router.push({
        name: "cityView",
        params: {
            city: state.city,
            country: state.country,
            state: state.state,
        },
        query: {
            id: state.id,
            lat: state.coords.lat,
            lon: state.coords.lon,
        },
    });
};
</script>

<style lang="scss" scoped></style>

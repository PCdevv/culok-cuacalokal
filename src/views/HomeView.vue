<template>
    <main class="container text-white">
        <div class="pt-4 mb-8 relative">
            <input
                type="text"
                v-model="searchQuery"
                @input="getSearchResult"
                placeholder="Search for city or state"
                class="py-2 px-1 w-full bg-transparent border-b focus:border-weather-secondary focus:outline-none focus:shadow-[0px_1px_0_0_#004E71]"
            />
            <ul
                class="absolute bg-weather-secondary text-white w-full shadow-md py-2 px-1 top-[66px]"
                v-if="weatherSearchResult"
            >
                <p v-if="searchError">
                    Sorry, something ment wrong. Please try again
                </p>
                <p v-if="!searchError && weatherSearchResult.length === 0">
                    No result match your query, try different term
                </p>
                <template v-else>
                    <li
                        class="py-2 cursor-pointer"
                        v-for="searchResult in weatherSearchResult"
                        :key="searchResult.lat"
                        @click="previewCity(searchResult)"
                    >
                        {{
                            `${searchResult.name}, ${searchResult.state}, ${searchResult.country}`
                        }}
                    </li>
                </template>
            </ul>
        </div>
        <div class="flex flex-col gap-4">
            <Suspense>
                <CityList>
                    <template #fallback>
                        <!-- <p>Loading</p> -->
                        <CityCardSkeleton />
                    </template>
                </CityList>
            </Suspense>
        </div>
    </main>
</template>

<script setup>
import { ref, Suspense } from "vue";
import { useRouter } from "vue-router";
import axios from "axios";
import CityList from "@/components/CityList.vue";
import CityCardSkeleton from "../components/CityCardSkeleton.vue";

const router = useRouter();
const previewCity = (searchResult) => {
    router.push({
        name: "cityView",
        params: {
            country: searchResult.country,
            state: searchResult.state,
            city: searchResult.name,
        },
        query: {
            lat: searchResult.lat,
            lon: searchResult.lon,
            preview: true,
        },
    });
};

const searchQuery = ref("");
const queryTimeout = ref(null);
const weatherSearchResult = ref(null);
const searchError = ref(null);

const getSearchResult = () => {
    clearTimeout(queryTimeout.value);
    queryTimeout.value = setTimeout(async () => {
        if (searchQuery.value !== "") {
            try {
                const result = await axios.get(
                    `${import.meta.env.VITE_BASE_URL}geo/1.0/direct?q=${
                        searchQuery.value
                    }&limit=5&appid=${import.meta.env.VITE_API_KEY}`
                );
                weatherSearchResult.value = result.data;
            } catch {
                searchError.value = true;
            }
            return;
        }
        weatherSearchResult.value = null;
    }, 300);
};
</script>

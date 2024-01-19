<template>
    <div class="flex flex-col flex-1 items-center">
        <!-- Banner -->
        <div class="text-white p-4 bg-weather-secondary w-full text-center">
            <p>
                You are currently previewing this city, click the "+" icon to
                start tracking this city.
            </p>
        </div>
        <!-- Weather Overview -->
        <div class="flex flex-col items-center text-white py-12">
            <h1 class="text-4xl mb-2">
                {{ route.params.city }}
            </h1>
            <p class="text-sm mb-12">
                {{
                    new Date(weatherData.data.currentTime).toLocaleDateString(
                        "en-us",
                        {
                            weekday: "short",
                            day: "2-digit",
                            month: "long",
                        }
                    )
                }}
                {{
                    new Date(weatherData.data.currentTime).toLocaleTimeString(
                        "en-us",
                        {
                            timeStyle: "short",
                        }
                    )
                }}
            </p>
            <p class="text-8xl mb-8">
                {{ Math.round(weatherData.data.main.temp) }}&deg;
            </p>
            <div class="text-center">
                <p>
                    Feels like
                    {{ Math.round(weatherData.data.main.feels_like) }}&deg;
                </p>
                <p class="capitalize">
                    {{ weatherData.data.weather[0].description }}
                </p>
                <img
                    :src="`http://openweathermap.org/img/wn/${weatherData.data.weather[0].icon}@2x.png`"
                    alt=""
                    class="h-auto w-[150px]"
                />
            </div>
        </div>
        <hr class="my-6 border-white border-opacity-10 border w-full" />
        <!-- Today Weather -->
        <div class="max-w-screen-md w-full py-12">
            <div class="mx-8 text-white">
                <h2 class="mb-4">Today Weather Forecast</h2>
                <div class="flex gap-10">
                    <div
                        v-for="hourData in weatherData.data.hourly"
                        :key="hourData.dt"
                        class="flex flex-col gap-4 items-center"
                    >
                        <p class="text-md whitespace-nowrap">
                            {{
                                new Date(hourData.dt_txt).toLocaleTimeString(
                                    "en-us",
                                    {
                                        hour: "numeric",
                                    }
                                )
                            }}
                        </p>
                        <img
                            :src="`http://openweathermap.org/img/wn/${hourData.weather[0].icon}@2x.png`"
                            alt=""
                            class="h-[50px] w-auto object-cover"
                        />
                        <p class="text-xl">
                            {{ Math.round(hourData.main.temp) }}&deg;
                        </p>
                    </div>
                </div>
            </div>
        </div>

        <div
            @click="removeCity"
            class="flex items-center gap-2 py-12 text-white cursor-pointer duration-150 hover:text-red-500"
        >
            <i class="fa-trash fa-solid"></i>
            <p>Remove City</p>
        </div>
    </div>
</template>

<script setup>
import { useRoute, useRouter } from "vue-router";
import axios from "axios";

const route = useRoute();
const getWeatherData = async () => {
    try {
        const weatherData = await axios.get(
            `${import.meta.env.VITE_BASE_URL}data/2.5/weather?lat=${
                route.query.lat
            }&lon=${route.query.lon}&appid=${
                import.meta.env.VITE_API_KEY
            }&units=metric`
        );

        const threeHourData = await axios.get(
            `${import.meta.env.VITE_BASE_URL}data/2.5/forecast?lat=${
                route.query.lat
            }&lon=${route.query.lon}&appid=${
                import.meta.env.VITE_API_KEY
            }&units=metric&cnt=8`
        );

        // waktu sekarang
        const localOffset = new Date().getTimezoneOffset() * 60000;
        const utc = weatherData.data.dt * 1000 + localOffset;
        weatherData.data.currentTime = utc + 1000 * weatherData.data.timezone;

        // cuaca perjam
        const hourly = threeHourData.data.list.map((hour) => {
            const utc = hour.dt * 1000 + localOffset;
            hour.currentTime = utc + 1000 * weatherData.data.timezone;
            return hour;
        });

        weatherData.data.hourly = hourly;
        // Flicker Delay
        // await new PromiseRejectionEvent((res) => setTimeout(res, 1000));

        return weatherData;
    } catch (error) {
        console.log(error);
    }
};
const weatherData = await getWeatherData();

const router = useRouter();
const removeCity = () => {
    const cities = JSON.parse(localStorage.getItem("savedCities"));
    const updatedCities = cities.filter((city) => city.id !== route.query.id);
    localStorage.setItem("savedCities", JSON.stringify(updatedCities));
    router.push({
        name: "home",
    });
};
</script>

<style lang="scss" scoped></style>

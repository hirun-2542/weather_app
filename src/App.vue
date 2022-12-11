<script setup>
import { ref, onMounted } from "vue";

const apiKey = "b1a4346043aab666a5e2afab14a66b0f";
const weather = ref({
    name: "",
    img: "",
    description: "",
    humidity: "",
    temp: "",
    wind: "",
});
const state = ref("");
const loading = ref(true);
const nodata = "card nodata";
const data = "card";
const fetchData = (city) => {
    state.value = "";
    fetch(
        `https://api.openweathermap.org/data/2.5/weather?q=${city}&appid=${apiKey}`
    )
        .then((response) => response.json())
        .then((data) => {
            loading.value = true;
            const { name } = data;
            const { icon, description } = data.weather[0];
            const { temp, humidity } = data.main;
            const { speed } = data.wind;
            weather.value.name = name;
            weather.value.img = `https://openweathermap.org/img/wn/${icon}.png`;
            weather.value.description = description;
            weather.value.humidity = humidity;
            weather.value.temp = Math.floor(temp - 273.15);
            weather.value.wind = speed;
            // load.value = false;
            // weather.value = data;
        })
        .catch((error) => {
            loading.value = false;
            console.log(error);
        });
};

onMounted(() => {
    fetchData("Bangkok");
});
</script>

<template>
    <div class="weather">
        <header class="search">
            <input
                type="text"
                placeholder="Search"
                v-model="state"
                @keydown.enter="fetchData(state)"
            />
            <button @click="fetchData(state)">
                <svg
                    stroke="currentColor"
                    fill="currentColor"
                    stroke-width="0"
                    viewBox="0 0 1024 1024"
                    height="1.5em"
                    width="1.5em"
                    xmlns="http://www.w3.org/2000/svg"
                >
                    <path
                        d="M909.6 854.5L649.9 594.8C690.2 542.7 712 479 712 412c0-80.2-31.3-155.4-87.9-212.1-56.6-56.7-132-87.9-212.1-87.9s-155.5 31.3-212.1 87.9C143.2 256.5 112 331.8 112 412c0 80.1 31.3 155.5 87.9 212.1C256.5 680.8 331.8 712 412 712c67 0 130.6-21.8 182.7-62l259.7 259.6a8.2 8.2 0 0 0 11.6 0l43.6-43.5a8.2 8.2 0 0 0 0-11.6zM570.4 570.4C528 612.7 471.8 636 412 636s-116-23.3-158.4-65.6C211.3 528 188 471.8 188 412s23.3-116.1 65.6-158.4C296 211.3 352.2 188 412 188s116.1 23.2 158.4 65.6S636 352.2 636 412s-23.3 116.1-65.6 158.4z"
                    ></path>
                </svg>
            </button>
        </header>
        <main :class="[loading ? data : nodata]">
            <h2>Weather in {{ weather.name }}</h2>
            <div class="temp">{{ weather.temp }}°C</div>
            <div class="flex">
                <img :src="weather.img" alt="" />
                <div class="description">{{ weather.description }}</div>
            </div>
            <div class="humiditly">Humidity: {{ weather.humidity }}%</div>
            <div class="wind">Wind speed: {{ weather.wind }} km/h</div>
        </main>
    </div>
</template>

<style scoped>
.weather {
    width: 25rem;
    margin: 0 auto;
    padding: 25px;
    color: white;
    background-color: #000000e0;
    border-radius: 20px;
    box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
    font-family: Arial, Helvetica, sans-serif;
}

.card {
    font-size: 120%;
    opacity: 0.8;
}

.search {
    display: flex;
    justify-content: center;
    align-items: center;
    margin-bottom: 20px;
}

.search input {
    border: none;
    outline: none;
    padding: 0.7em 1em;
    border-radius: 25px;
    background: #7c7c7c2d;
    color: white;
    font-size: 110%;
    width: calc(100% - 100px);
}
.search button {
    margin: 0.5rem;
    border-radius: 50%;
    border: none;
    height: 3em;
    width: 3em;
    background: #7c7c7c2d;
    color: white;
    cursor: pointer;
    transition: all 0.2s ease-in-out;
}
.search button:hover {
    background: #7c7c7c6d;
    cursor: pointer;
}

.temp {
    font-size: 3rem;
    font-weight: bold;
    margin: 0.5rem 0;
}

.flex {
    display: flex;
    align-items: center;
    margin: 0.5rem 0;
}
.description {
    text-transform: capitalize;
}

.humiditly {
    margin: 0.5rem 0;
}

.card.nodata {
    visibility: hidden;
    max-height: 20px;
    position: relative;
}

.card.nodata:after {
    visibility: visible;
    content: "No Data";
    color: white;
    position: absolute;
    top: 0;
    left: 40%;
    font-size: 120%;
}
</style>

<script setup>
import { ref } from "vue";
import axios from "axios";

const city = ref("");
const currentWeather = ref({});
const fetchingCurrentWeather = ref(false);
const isNoMatchingLocationFound = ref(false);

async function fetchCurrentWeather() {
  try {
    fetchingCurrentWeather.value = true;
    const currentWeatherResponse = await axios.get(
      `http://api.weatherapi.com/v1/current.json?key=511c1a9099544abd96b184841252102&q=${city.value}&aqi=no`
    );

    currentWeather.value = currentWeatherResponse.data;
    fetchingCurrentWeather.value = false;
    isNoMatchingLocationFound.value = false;
  } catch (e) {
    console.error(e.response.data.error);
    if (e.response.data.error.code === 1006) {
      isNoMatchingLocationFound.value = true;
      fetchingCurrentWeather.value = false;
    }
  }
}
</script>

<template>
  <div
    class="page-wrapper"
    :style="{
      backgroundColor:
        Object.keys(currentWeather).length === 0
          ? void 0
          : currentWeather?.current?.is_day
          ? '#0093E9'
          : '#2b4162',
      backgroundImage:
        Object.keys(currentWeather).length === 0
          ? void 0
          : currentWeather?.current?.is_day
          ? 'linear-gradient(160deg, #0093E9 0%, #80D0C7 100%)'
          : 'linear-gradient(315deg, #2b4162 0%, #12100e 74%)',
    }"
  >
    <h1
      class="page-title"
      :style="{
        color: Object.keys(currentWeather).length !== 0 ? 'white' : void 0,
      }"
    >
      Type the city to see its current weather:
    </h1>
    <div class="input-wrapper">
      <input class="city-input" v-model="city" />
      <button class="fetch-weather-button" @click="fetchCurrentWeather">
        {{ fetchingCurrentWeather ? "..." : "Fetch weather data" }}
      </button>
    </div>
    <div
      v-if="Object.keys(currentWeather).length !== 0"
      class="weather-wrapper"
    >
      <h2 class="weather-title">
        Current weather in {{ currentWeather.location.name }},
        {{ currentWeather.location.country }}:
      </h2>
      <div class="local-time-text">
        <u>Local time</u>:
        {{
          new Date(
            currentWeather.location.localtime_epoch * 1000
          ).toLocaleString("de-DE")
        }}
      </div>
      <div class="temperature-text">
        <u>Temperature</u>: {{ currentWeather.current.temp_c }}° C /
        {{ currentWeather.current.temp_f }}° F
      </div>
      <div class="feels-liketext">
        <u>Feels like</u>: {{ currentWeather.current.feelslike_c }}° C /
        {{ currentWeather.current.feelslike_f }}° F
      </div>
      <div class="wind-text">
        <u>Wind</u>: {{ currentWeather.current.wind_kph }} kph /
        {{ currentWeather.current.wind_mph }}° mph, <u>Degree</u>:
        {{ currentWeather.current.wind_degree }}
      </div>
      <div class="humidity-text">
        <u>Humidity</u>: {{ currentWeather.current.humidity }}%
      </div>
      <div class="condition-wrapper">
        <img
          class="condition-icon"
          :src="currentWeather.current.condition.icon"
        />
        <div class="condition-text">
          {{ currentWeather.current.condition.text }}
        </div>
      </div>
      <div class="last-updated-text">
        <u>Last updated</u>:
        {{
          new Date(currentWeather.current.last_updated).toLocaleString("de-DE")
        }}
      </div>
    </div>
    <div class="no-matching-location-wrapper" v-if="isNoMatchingLocationFound">
      No matching location found, please try another city name.
    </div>
  </div>
</template>

<style scoped>
.page-wrapper {
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 60px;
  height: 100%;
}
.page-title {
  margin: 0 0 40px;
  font-family: "Lora";
  font-weight: 600;
}
.input-wrapper {
  display: flex;
  gap: 8px;
}
.city-input {
  width: 150px;
  font-family: "Lora";
  border-radius: 0.25rem;
  border: 1px solid rgba(0, 0, 0, 0.1);
  padding-left: 12px;
  box-sizing: border-box;
  font-weight: 600;
  line-height: 1.25;
  box-shadow: rgba(0, 0, 0, 0.02) 0 1px 3px 0;
  font-size: 16px;
  color: rgba(0, 0, 0, 0.85);
}
.fetch-weather-button {
  align-items: center;
  background-color: #ffffff;
  border: 1px solid rgba(0, 0, 0, 0.1);
  border-radius: 0.25rem;
  box-shadow: rgba(0, 0, 0, 0.02) 0 1px 3px 0;
  box-sizing: border-box;
  color: rgba(0, 0, 0, 0.85);
  cursor: pointer;
  display: inline-flex;
  font-family: "Lora";
  font-size: 16px;
  font-weight: 600;
  justify-content: center;
  line-height: 1.25;
  margin: 0;
  min-height: 3rem;
  padding: calc(0.875rem - 1px) calc(1.5rem - 1px);
  position: relative;
  text-decoration: none;
  transition: all 250ms;
  user-select: none;
  -webkit-user-select: none;
  touch-action: manipulation;
  vertical-align: baseline;
  width: auto;
}
.fetch-weather-button:hover,
.fetch-weather-button:focus {
  border-color: rgba(0, 0, 0, 0.15);
  box-shadow: rgba(0, 0, 0, 0.1) 0 4px 12px;
  color: rgba(0, 0, 0, 0.65);
}

.fetch-weather-button:hover {
  transform: translateY(-1px);
}

.fetch-weather-button:active {
  background-color: #f0f0f1;
  border-color: rgba(0, 0, 0, 0.15);
  box-shadow: rgba(0, 0, 0, 0.06) 0 2px 4px;
  color: rgba(0, 0, 0, 0.65);
  transform: translateY(0);
}
.weather-wrapper {
  margin-top: 40px;
  color: white;
}
.weather-title {
  margin: 0 0 20px;
  text-align: center;
  text-decoration: underline;
}
.condition-wrapper {
  display: flex;
  gap: 12px;
  justify-content: center;
  align-items: center;
}
.condition-icon {
  height: 40px;
  width: 40px;
}
.local-time-text,
.temperature-text,
.feels-liketext,
.wind-text,
.humidity-text {
  text-align: center;
  margin-bottom: 20px;
  font-size: 20px;
}
.condition-text {
  text-align: center;
  font-size: 20px;
}
.last-updated-text {
  text-align: center;
  font-size: 20px;
  margin-top: 40px;
}
.no-matching-location-wrapper {
  margin-top: 40px;
  font-size: 20px;
  font-weight: 600;
}
</style>

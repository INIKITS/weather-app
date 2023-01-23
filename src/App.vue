<template>
  <div class="min-h-full flex flex-col items-center bg-cover bg-gradient-to-r from-cyan-500 to-blue-500">
    <header>
      
      <h1 class="text-center">Weather App</h1>
    </header>
    <div id="search-container" class="mt-6 flex flex-row justify-center gap-4">
      <input id="search-bar " v-model="query" class="rounded-full opacity-70 focus:outline-none" type="text" />
      <button type="button" id="search-button" @click="fetchLocation"
        class="opacity-70 bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded-full">
        button
      </button>
    </div>
    <span id="city-name" class="pt-3">location: {{location}}</span>

    <div id="weather-container" class="pt-6 ">
      <span id="temperature">temp: {{ weather.temp }} </span>
      <!-- <span v-if=!weather.isDaytime id="temperature-icon">icon</span>
      <span v-else >it not daytime</span> -->
      <div id="current-conditions">current: {{weather.shortForecast}}</div>
    </div>

  </div>
</template>

<script>
const publicEnvVar = process.env.VUE_APP_PUBLIC_API;
const publicEnvVar2 = process.env.VUE_APP_PUBLIC_API_2;
const baseUrl = process.env.VUE_APP_BASE_URL;
const coordinatesUrl = process.env.VUE_APP_COORDINATE_URL;

export default {
  name: "App",
  data() {
    return {
      password: publicEnvVar,
      password2: publicEnvVar2,
      baseUrl: baseUrl,
      coordinatesUrl: coordinatesUrl,
      coordinatesLat: null,
      coordinatesLong: null,
      query: "",
      weather: {
        temp: null,
        shortForecast: null,
        isDaytime: null,
      },
      location: null,
    };
  },

  methods: {
    async fetchLocation() {
      try {
        const response = await fetch(
          this.coordinatesUrl +
          this.query +
          "&limit=1" +
          "&appid=" +
          this.password
        );
        const data = await response.json();

        this.location = data[0].name;
        this.coordinatesLat = data[0].lat;
        this.coordinatesLong = data[0].lon;

        this.fetchWeather(this.coordinatesLat, this.coordinatesLong);
      } catch (err) {
        console.error(err.message);
      }
    },
    async fetchWeather(lat, long) {
      try {
        const response = await fetch(
          this.baseUrl +
          lat +
          "," +
          long,
          {
            method: 'GET',
            headers: {
              'User-Agent': 'vuetestweatherapp, dakota.h.solis@gmail.com',
            }
          }
        );
        const data = await response.json();

        const weatherJson = await fetch (data.properties.forecast);

        const weatherData = await weatherJson.json();

        const weather = weatherData.properties.periods[0]

        this.weather.temp = weather.temperature + ' ' + weather.temperatureUnit;
        this.weather.shortForecast = weather.shortForecast;
        this.weather.isDaytime = weather.isDaytime;

      } catch (err) {
        console.error(err.message);
      }
    },
  },
};
// }
</script>

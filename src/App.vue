<template>
  <div>
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
    <span id="city-name">{{location}}</span>

    <div id="weather-container">
      <span id="temperature">{{ weather.temp }} </span>
      <!-- <span id="temperature-icon">icon</span> -->
      <div id="current-conditions">{{weather.shortForecast}}</div>
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

        console.log("location :>> ", this.location);
        console.log("coordinatesLat :>> ", this.coordinatesLat);
        console.log("coordinatesLong :>> ", this.coordinatesLong);
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

        console.log("data :>> ", data.properties.forecast);

        const weatherJson = await fetch (data.properties.forecast);

        const weatherData = await weatherJson.json();

        console.log('weather props:>> ', weatherData.properties.periods);

        const weather = weatherData.properties.periods[0]

        console.log('short :>> ', weather.shortForecast);
        console.log('temperature :>> ', weather.temperature + ' ' + weather.temperatureUnit);
        console.log('dayTime :>> ', weather.isDaytime);
        console.log('weather :>> ', weather);

        this.weather.temp = weather.temperature + ' ' + weather.temperatureUnit;
        this.weather.shortForecast = weather.shortForecast;
        this.weather.isDaytime = weather.isDaytime;

        console.log('typeof(weather.isDaytime) :>> ', typeof(weather.isDaytime));

      } catch (err) {
        console.error(err.message);
      }
    },
  },
};
// }
</script>

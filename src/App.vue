<script setup>
import { ref, computed } from "vue";

const date = new Date();

let day = date.getDate();
let month = date.toLocaleString('default', { month: 'long' });
let year = date.getFullYear();
let currentDate = `${month} ${day}, ${year}`;

const api_key = ref("33783d8a633da7dfc86a19a79cf0bdee");
const url_base = ref("https://api.openweathermap.org/data/2.5/");

let query = ref("");
let weather = ref({});
let roundedTemp = ref();

const isCold = computed(() => {
  return roundedTemp.value <= 32 ? true : false
});

const isFair = computed(() => {
  return 32 < roundedTemp.value && roundedTemp.value < 70 ? true : false
});

const isWarm = computed(() => {
  return roundedTemp.value >= 70 ? true : false
});


function getWeather(e) {
  if (e.key === "Enter") {
    fetch(`${url_base.value}weather?q=${query.value}&appid=${api_key.value}&units=imperial`)
      .then((res) => {
        return res.json();
      })
      .then(setResults);
  }
}

function setResults(results) {
  weather.value = results;
  roundedTemp.value = Math.round(weather.value.main.temp);
  console.log(isCold.value, isFair.value, isWarm.value);
  console.log(roundedTemp.value);

}

</script>

<template>
  <div :class="{
    'app': isCold,
    'app-fair': isFair,
    'app-warm': isWarm,
  }">
    <main>
      <div class="search-box">
        <input
          type="text"
          class="search-bar"
          placeholder="search by city name..."
          v-model="query"
          @keyup="getWeather"
        />
      </div>

      <div class="weather-wrap" v-if="typeof weather.main !== 'undefined'">
        <div class="location-box">
          <div class="location">
            {{ weather.name }}
          </div>
          <div class="date">{{ currentDate }}</div>
        </div>

        <div class="weather-box">
          <div class="temp">{{ roundedTemp }}&degF</div>
          <div class="weather">{{ weather.weather[0].main }}</div>
        </div>
      </div>

      <div class="no-data" v-else>Sorry! No information available. Please enter a city name or check the current spelling.</div>
    </main>
  </div>
</template>

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
body {
  font-family: "montserrat", sans-serif;
}
.app {
  background-image: url("./assets/cold-bg.jpg");
  background-size: cover;
  background-position: bottom;
  transition: 0.4s;
}

.app-fair {
  background-image: url("./assets/fair-bg.jpg");
  background-size: cover;
  background-position: bottom;
  transition: 0.4s;
}

.app-warm {
  background-image: url("./assets/warm-bg.jpg");
  background-size: cover;
  background-position: bottom;
  transition: 0.4s;
}

main {
  min-height: 100vh;
  padding: 25px;
  background-image: linear-gradient(
    to bottom,
    rgba(0, 0, 0, 0.25),
    rgba(0, 0, 0, 0.75)
  );
}
.search-box {
  width: 100%;
  margin-bottom: 30px;
}
.search-box .search-bar {
  display: block;
  width: 100%;
  padding: 15px;

  color: #313131;
  font-size: 20px;
  appearance: none;
  border: none;
  outline: none;
  background: none;
  box-shadow: 0px 0px 8px rgba(0, 0, 0, 0.25);
  background-color: rgba(255, 255, 255, 0.5);
  border-radius: 0px 16px 0px 16px;
  transition: 0.4s;
}
.search-box .search-bar:focus {
  box-shadow: 0px 0px 16px rgba(0, 0, 0, 0.25);
  background-color: rgba(255, 255, 255, 0.75);
  border-radius: 16px 0px 16px 0px;
}
.location-box .location {
  color: #fff;
  font-size: 32px;
  font-weight: 500;
  text-align: center;
  text-shadow: 1px 3px rgba(0, 0, 0, 0.25);
}
.location-box .date {
  color: #fff;
  font-size: 20px;
  font-weight: 300;
  font-style: italic;
  text-align: center;
}
.weather-box {
  text-align: center;
}
.weather-box .temp {
  display: inline-block;
  padding: 10px 25px;
  color: #fff;
  font-size: 102px;
  font-weight: 900;
  text-shadow: 3px 6px rgba(0, 0, 0, 0.25);
  background-color: rgba(255, 255, 255, 0.25);
  border-radius: 16px;
  margin: 30px 0px;
  box-shadow: 3px 6px rgba(0, 0, 0, 0.25);
}
.weather-box .weather {
  color: #fff;
  font-size: 48px;
  font-weight: 700;
  font-style: italic;
  text-shadow: 3px 6px rgba(0, 0, 0, 0.25);
}

.no-data {
  text-align: center;
  font-weight: bold;
  color: white;
}
</style>

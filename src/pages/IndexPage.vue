<template>
  <q-page class="flex column">
    <div class="col q-pt-lg q-px-md">
      <q-input v-model="search" @keyup.enter="getWeatherBySearch" label="Search" borderless dark>
        <template v-slot:before>
          <q-icon
            @click="getLocation"
            name="my_location" />
        </template>

        <template v-slot:append>
          <q-btn round dense flat icon="search" @click="getWeatherBySearch" />
        </template>
      </q-input>
    </div>

    <template v-if="weatherDisplay">
      <div class="col text-white text-center">
        <div class="text-h4 text-weight-light">
          {{weatherData.name}}
        </div>
        <div class="text-h6 text-weight-light">
          {{weatherData.weather[0].main}}
        </div>
        <div class="text-h1 text-weight-thin q-my-lg relative-position">
          <span>{{Math.round(weatherData.main.temp)}}</span>
          <span class="text-h4 relative-position degree">&deg;C</span>
        </div>
      </div>

      <div class="col text-center">
        <img src="`https://openweathermap.org/img/wn/${ weatherData.weather[0].icon }@2x.png`">
      </div>
    </template>

    <template v-else>
      <div class="col column text-center text-white">
        <div class="col text-h2 text-weight-thin">
          Quasar <br> Weather
        </div>
        <q-btn
          @click="getLocation"
          class="col"
          flat>
          <q-icon left size="3em" name="my_location" />
          <div>Find my location</div>
        </q-btn>
      </div>
    </template>

    <div class="col skyline" />
  </q-page>
</template>

<script>
import {defineComponent, ref} from 'vue';
import {api} from "boot/axios";

export default defineComponent({
  name: 'IndexPage',

  setup(){

      let lat, long;
      let search = ref('');
      let weatherDisplay = ref(false);
      let weatherData = ref(null);
      let apiURL = 'https://api.openweathermap.org/data/2.5/weather';
      let apiKey = ''; //add your own apiKey

      function getLocation(){
      navigator.geolocation.getCurrentPosition(position => {
        lat = position.coords.latitude
        long = position.coords.longitude
        getWeatherByCoords()
      })
    };

    function getWeatherByCoords(){
      api.get(`${apiURL}?lat=${lat}&long=${long}&appid=${apiKey}&units=metric`).then(response => {
        weatherData = response.data
      })
      weatherDisplay = true
    };

    function getWeatherBySearch(){
      api.get(`${apiURL}?q=${ search.value }&appid=${apiKey}&units=metric`).then(response => {
        weatherData = response.data
      })
      weatherDisplay = true
    };

    return {
      search,
      getLocation,
      weatherData,
      getWeatherBySearch,
      weatherDisplay,
    };
  }
})
</script>

<style lang="sass">
.q-page
  background: linear-gradient(to bottom, #2C5364, #203A43, #0F2027)
.degree
  top: -44px
.skyline
  flex: 0 0 100px
  background: url(../statics/town.png) center bottom
  background-size: contain
</style>


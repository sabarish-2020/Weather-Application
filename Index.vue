<template>
  <q-page class="flex column" :class="bgClass">
   <div class="col q-pt-lg q-px-md">
     <q-input 
        v-model="search"
        @keyup.enter="getweatherBySearch" 
        placeholder="Search" 
        dark
        borderless
    >
        <template v-slot:before>
          <q-icon 
            @click="getLocation"
            name="My_Location" />
        </template>

        <template v-slot:append>
          <q-btn
          @click="getweatherBySearch"
          icon="search"
          dense
          flat
          round
          />
        </template>
      </q-input>
   </div>

    <template v-if="weatherData">
    <div class="col text-white text-center">
     <div class="test-h4 text-weight-light">
       {{ weatherData.name }}
     </div>
     <div class="text-h6 text-weight-light">
       {{ weatherData.weather[0].main }}
      <div class="text-h1 text-weight-thin 
      q-my-lg relative-position">
        <span>{{ Math.round(weatherData.main.temp) }}</span>
        <span class="text-h4 relative-position
        degree">&deg;C</span>

      </div>
     </div>
   </div>
   <div class="col text-center">
    <img :src="'https://openweathermap.org/img/wn/${ weatherData.weather[0].icon }@2x.png'" >
   </div>
    </template>

   <template v-else>
    <div class="col column text-center
    text-white">
    <div class= "col text-h2
    text-weight-thin">
      GoodDay<br>Weather
    </div>
    <q-btn 
    @click="getLocation"
    class="col" 
    flat
    >
      <q-icon left size="3em" name="my_location" />
      <div>Look for My Location</div>
    </q-btn>

  </div>
</template>

   <div class="col skyline"></div>
  </q-page>
</template>

<script>
export default {
  name: 'PageIndex',
  data() {
    return {
      search: '',
      weatherData: null,
      lat: null,
      lon: null,
      apiUrl: 'https://api.openweathermap.org/data/2.5/weather',
      apiKey: '7de223cf82185d8c3f22eb506740b232'
    }
  },
  computed: {
    bgClass() {
      if (this.weatherData) {
        if (this.weatherData[0].icon.endsWith('n')) {
          return 'bg-night'
        }
        else {
          return 'bg-day'
        }
      }
    }
  },
  methods: {
    getLocation() {
      this.$q.loading.show()

      if (this.$q.platform.is.electron) {
          this.$axios.get('https://freegeoip.app/json/').the(response => {
            this.lat = response.data.latitude
            this.lon = position.data.longitude
            this.getweatherByCoords()
          })
      }

      else {
        navigator.geolocation.getCurrentPosition(position => {
        console.log('position: ', position)
        this.lat = position.coords.latitude
        this.lon = position.coords.longitude
        this.getweatherByCoords()
      })

      }
      
    },
    getweatherByCoords() {
      this.$q.loading.show()
      this.$axios('${ this.apiUrl }?lat=${ this.lat }&lon=${ this.lon }&appid=${ this.apiKey }&units=metric').then(reponse => {
        this.weatherData = response.data
        this.$q.loading.hide()
      })
    },
     getweatherBySearch() {
       this.$q.loading.show()
       console.log('getWeatherBySearch')
      this.$axios('${ this.apiUrl }?q={ this.search }&appid=${ this.apiKey }').then(reponse => {
        this.weatherData = response.data
        this.$q.loading.hide()
       })

    }
  }
}
</script>

<style lang="sass">
  .q-page 
    background: linear-gradient(to bottom, 
    #267871, #136a8a)
    &.bg-night
      background: linear-gradient(to bottom, #232526, #414345)
    &.bg-day
      background: linera-gradient(to bottom, #00b4db, #0083b0)
    .degree
      top: -44px
    .skyline
      flex: 00 100px
      background: url(../statics/icons/skyline.png)
      background-size: contain
      background-position: center bottom
</style>
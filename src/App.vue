<template>
  <div id="app">
    <main :class="imageClass">
      <div class="container">
        <div class="container__search">
          <input 
          type="text" 
          name="searchBar" placeholder="Your city" class="container__searchInput"
          v-model="search"
          @keypress="getWeatherData">
        </div>
      
      <div class="container__data" v-if="typeof weatherInfo.main != 'undefined'">
        <div class="container__data cityDate">
          {{this.getDate()}}
        </div>
        <div class="container__data cityName">
          {{weatherInfo.name}}, {{weatherInfo.sys.country}}
        </div>
        <div class="container__data cityTemp">
          {{this.toCelsius(weatherInfo.main.temp)}}
        </div>
        <div class="container__data cityWeather">
          {{weatherInfo.weather[0].main}}
        </div>
      </div>
      </div>
    </main>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  name: 'App',
  data() {
    return {
      search: '',
      weatherInfo: {},
      base_url: 'api.openweathermap.org/data/2.5/',
      api_key: '586af6ed8ad394dfed8df5586930e7d7',
      imageClass: 'main'
    }
  },
  methods: {
    getWeatherData (event) {
      if(event.key == "Enter") {
        axios.get(`http://api.openweathermap.org/data/2.5/weather?q=${this.search}&APPID=${this.api_key}`)
        .then(response => {
          this.weatherInfo = response.data;
        })
      }
    },
    toCelsius(temp) {
      return Math.round((temp - 273));
    },
    getDate() {
      let d = new Date(),
          months = ['January', 'February', 'March', 'April', 'May', 'June', 'July', 'August', 'September', 'October', 'November', 'December'],
          days = ['Sunday', 'Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday', 'Saturday'],
          day = days[d.getDay()],
          date = d.getDate(),
          month = months[d.getMonth()],
          year = d.getFullYear();

      return `${day}, ${date} ${month} ${year}`
    }
  },
  watch: {
    weatherInfo: function() {
      if (this.toCelsius(this.weatherInfo.main.temp) > 23) {
        this.imageClass = 'main warm';
      } else if (this.toCelsius(this.weatherInfo.main.temp) < 15) {
        this.imageClass = 'main cold'
      } else {
        this.imageClass = 'main'
      }
    }
  }
}
</script>

<style lang="scss">
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box
}

body {
  font-family: 'poppins', sans-serif;
  color: rgba(255, 255, 255, 1);
}

.main {
  width: 100%;
  height: 100%;
  background: url('./assets/cloudy-bg.jpg');
  background-origin: bottom;
  background-size: 100% 100%;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}

.warm {
  background: url('./assets/warm-bg.jpg');
  background-size: 130% 100%;
}

.cold {
  background: url('./assets/cold-bg.jpg');
  background-size: 140% 100%;
  background-position: bottom right;
}

.container {
  display: flex;
  flex-direction: column;
  align-items: center;
  width: 80%;
  min-height: 100vh;
  text-align: center;

  &__search {
    margin: 75px 0 5px 0;
    width: 100%;

    &Input {
      padding: 15px 25px;
      font-size: 1rem;
      outline: none;
      border: none;
      border-radius: 10px;
      transition: .3s;
      color: rgba(0, 0, 0, 0.5);

      &::placeholder {
        color: rgba(0, 0, 0, 0.5);  
      }

      &:focus {
        box-shadow: 2px 2px 0 rgba(0, 0, 0, 0.25);
      }
    }
  }

  &__data {
    display: flex;
    flex-direction: column;
    align-items: center;

    & .cityDate {
      font-size: 1.2rem;
    }

    & .cityName {
      margin: 80px 0 30px 0;
      font-size: 2rem;
      font-weight: 700;
    }

    & .cityTemp {
      width: 70%;
      font-size: 3rem;
      font-weight: 900;
      background-color: rgba(255, 255, 255, 0.5);
      border-radius: 10px;
      box-shadow: 2px 4px 0 rgba(0, 0, 0, 0.25)
    }

    & .cityWeather {
      font-size: 1.8rem;
      font-weight: 700;
      margin-top: 20px;
    }
  }
}
</style>

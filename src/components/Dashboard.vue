<template>
  <main>
    <div>
      <form
        @submit.prevent="processInput"
        class="search-bar"
        autocomplete="off"
      >
        <input
          type="text"
          v-model="input"
          name="input"
          placeholder="Search..."
          class="search-bar__input"
          aria-label="search"
        />
        <button class="search-bar__submit">
          <i class="fas fa-search" aria-label="submit search"></i>
        </button>
      </form>
      <h1 class="title animate__animated animate__zoomInUp" v-if="!recieved">
        VUE The Weather
      </h1>
      <div class="container">
        <div class="locDate">
          <h1 v-if="recieved" class="location">
            {{ location.name }},{{ location.region }}
          </h1>
          <div class="date">{{ dateBuilder() }}</div>
        </div>

        <div
          v-if="recieved"
          class="currentWrapper animate__animated animate__slideInLeft"
        >
          <h3 v-if="recieved" class="temp">
            {{ Math.round(weather.temp_f) }}&deg;
          </h3>

          <div class="dataWrap">
            <h3 v-if="recieved" class="currentWeather">{{ current.text }}</h3>
            <div :style="styles" class="icon"></div>
          </div>
          <h3 v-if="recieved" class="feels">
            Feels Like {{ weather.feelslike_f }}&deg; F
          </h3>
          <h3 v-if="recieved" class="feels">
            Humidity {{ weather.humidity }}%
          </h3>
          <h4 v-if="recieved" class="feels">
            Wind speed {{ weather.wind_mph }} Mph
          </h4>
        </div>

        <!-- Forecast -->
        <div class="forecastWrap">
          <div v-for="(data, index) in forecast" :key="index" class="day">
            <h4 class="dates">{{ data.date }}</h4>
            <div :style="iconStyles" class="icon"></div>
            <div class="hiLowWrap">
              <p class="hiTemp">Hi {{ Math.round(data.day.maxtemp_f) }}&deg;</p>
              <p class="lowTemp">
                Low {{ Math.round(data.day.mintemp_f) }}&deg;
              </p>
            </div>
            <p>{{ data.day.condition.text }}</p>
            <p>Chance of rain {{ data.day.daily_chance_of_rain }}%</p>
            <p>Sunrise {{ data.astro.sunrise }}</p>
            <p>Sunset {{ data.astro.sunset }}</p>
          </div>
        </div>
      </div>
    </div>
  </main>
</template>

<script>
export default {
  name: "Dashboard",

  data() {
    return {
      key: process.env.VUE_APP_API_KEY,
      location: {},
      weather: {},
      forecast: [],
      current: {},
      input: "",
      recieved: false
    };
  },

  computed: {
    styles() {
      return {
        background: `url(${this.current.icon})`
      };
    },
    iconStyles() {
      return {
        background: `url(${this.forecast[0].day.condition.icon})`,
        margin: `auto`
      };
    }
  },

  methods: {
    fetchData: async function() {
      try {
        const res = await fetch(
          `https://api.weatherapi.com/v1/current.json?key=${this.key}&q=${this.input}`
        );
        const location = await res.json();
        this.location = location.location;
        this.weather = location.current;
        this.current = this.weather.condition;
      } catch (e) {
        console.log(e);
      }
    },

    dateBuilder: function() {
      let d = new Date();
      let months = [
        "January",
        "Febuary",
        "March",
        "April",
        "May",
        "June",
        "July",
        "August",
        "September",
        "October",
        "November",
        "December"
      ];
      let days = [
        "Monday",
        "Tuesday",
        "Wednesday",
        "Thursday",
        "Friday",
        "Saturday",
        "Sunday"
      ];
      let day = days[d.getDay()];
      let date = d.getDate();
      let month = months[d.getMonth()];
      let year = d.getFullYear();
      return `${day} ${date} ${month} ${year}`;
    },
    processInput: function() {
      if (!this.input) return;
      this.fetchData();
      this.fetchForecast();
      this.recieved = true;
      this.input = "";
    },

    fetchForecast: async function() {
      const res = await fetch(
        `https://api.weatherapi.com/v1/forecast.json?key=${this.key}&q=${this.input}&days=5`
      );
      const forecast = await res.json();
      this.forecast = forecast.forecast.forecastday;
    }
  }
};
</script>

<style lang="scss" scoped>
/* search styles */
.fas {
  width: 100%;
  height: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
}
.search-bar {
  --size: 60px;
  border: 2px solid tomato;
  display: flex;
  border-radius: 50px;
  height: var(--size);
  width: var(--size);
  padding: 3px;
  position: relative;
  transition: 300ms cubic-bezier(0.18, 0.79, 0.2, 1.14);
  overflow: hidden;
  margin: 30px auto;

  &__input {
    flex-grow: 1;
    font-size: 1.5rem;
    padding: 0 0.5em;
    border: 0;
    opacity: 0;
    position: absolute;
    top: 0;
    bottom: 0;
    left: 0;
    line-height: calc(var(--size) -3px);
    cursor: pointer;
    &:focus {
      outline: 0;
    }
  }
  &__submit {
    font-size: 1.5rem;
    cursor: pointer;
    border: 0;
    background: transparent;
    border-radius: 50%;
    width: calc(var(--size) - 10px);
    height: calc(var(--size) - 10px);
    margin-left: auto;
    transition: background 150ms ease-in-out;
  }
  &:focus-within {
    width: 100%;

    .search-bar__input {
      opacity: 1;
      cursor: initial;
      background: transparent;
      width: calc(100% - var(--size));
    }

    .search-bar__submit {
      background: tomato;
      color: white;

      &:focus,
      &:hover {
        outline: 0;
        box-shadow: 0px 0px 10px rgba(0, 0, 0, 0.35);
      }
    }
  }
}

main {
  background-image: linear-gradient(
    to bottom,
    rgba(0, 0, 0, 0.25),
    rgba(0, 0, 0, 0.75)
  );
  height: 100vh;
  padding: 20px;
}
.title {
  position: fixed;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  color: #fefefe;
  text-shadow: 1px 3px rgba(0, 0, 0, 0.55);
  font-size: 5rem;
}
.container {
  display: flex;
  justify-content: center;
  flex-direction: column;
  align-items: center;
  text-align: center;
}
.feels,
.time {
  text-align: center;
  padding: 10px 0px;
}
.location {
  color: #fefefe;
  font-size: 32px;
  font-weight: 500;
  text-align: center;
  text-shadow: 1px 3px rgba(0, 0, 0, 0.25);
  padding: 20px;
}

.locDate .date {
  color: #fefefe;
  font-size: 20px;
  font-weight: 300;
  text-align: center;
  font-style: oblique;
  padding-bottom: 20px;
}
.temp {
  display: flex;
  justify-content: center;

  padding: 10px 25px;
  color: #fefefe;
  font-size: 102px;
  font-weight: 900;
  text-shadow: 3px 6px rgba(0, 0, 0, 0.25);
}
.dataWrap {
  display: flex;
  justify-content: center;
  flex-direction: column;
  align-items: center;
  width: 100%;
}
.dataWrap .currentWeather {
  color: #fefefe;
  font-size: 38px;
  font-weight: bold;
  font-style: italic;
  text-shadow: 3px 6px rgba(0, 0, 0, 0.25);
}
.icon {
  width: 65px;
  height: 65px;
}

.currentWrapper {
  background-color: rgba(0, 0, 0, 0.5);
  border-radius: 8px;
  margin: 30px 0;
  box-shadow: 3px 6px 10px rgba(0, 0, 0, 0.25);
  padding: 20px 80px;
  color: #fefefe;
}

.forecastWrap {
  display: flex;
  justify-content: center;
  align-items: flex-start;
  flex-wrap: wrap;
}
.forecastWrap .day {
  background-color: rgba(0, 0, 0, 0.5);
  border-radius: 8px;
  margin: 10px 10px;
  box-shadow: 3px 6px 10px rgba(0, 0, 0, 0.25);
  padding: 20px 50px;
  color: #fefefe;
}
.hiTemp,
.lowTemp {
  font-weight: bold;
  font-size: 1.2rem;
}
.hiTemp,
.lowTemp,
.dates,
p {
  margin: 10px 0px;
}
.hiLowWrap {
  box-shadow: 0px 0px 10px rgba(255, 255, 255, 0.25);
  padding: 10px;
  border-radius: 10px;
}
.dates {
  border-bottom: 0.5px solid white;
  padding-bottom: 5px;
}
</style>

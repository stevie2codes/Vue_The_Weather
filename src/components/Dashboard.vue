<template>
  <main>
    <div>
      <form @submit.prevent="processInput">
        <input type="text" v-model="input" name="input" placeholder="Search.." />
      </form>
      <div class="container">
        <div class="locDate">
          <h1 v-if="recieved" class="location">
            {{location.
            name}},{{location.region}}
          </h1>
          <div class="date">Todays date</div>
        </div>
        <div v-if="recieved" class="currentWrapper">
          <h3 v-if="recieved" class="temp">{{Math.round(weather.temp_f)}}&deg;</h3>

          <div class="dataWrap">
            <h3 v-if="recieved">{{current.text}}</h3>
            <div :style="styles" class="icon"></div>
          </div>
          <h3 v-if="recieved" class="feels">Feels Like {{weather.feelslike_f}}&deg; F</h3>
          <h3 v-if="recieved" class="feels">Humidity {{weather.humidity}}%</h3>
          <h4 v-if="recieved" class="feels">Wind speed {{weather.wind_mph}} Mph</h4>
        </div>

        <!-- Forecast -->
        <div class="forecastWrap">
          <div v-for="(data,index) in forecast" :key="index" class="day">
            <h4 class="dates">{{data.date}}</h4>
            <p class="hiTemp">Hi {{Math.round(data.day.maxtemp_f)}}</p>
            <p class="lowTemp">Low {{Math.round(data.day.mintemp_f)}}</p>
            <p>Chance of rain {{data.day.daily_chance_of_rain}}%</p>
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
    }
  },

  methods: {
    fetchData: async function() {
      try {
        const res = await fetch(
          `http://api.weatherapi.com/v1/current.json?key=484a4fd44fb94f04b3a191835200108&q=${this.input}`
        );
        const location = await res.json();
        this.location = location.location;
        this.weather = location.current;
        this.current = this.weather.condition;
      } catch (e) {
        console.log(e);
      }
    },
    processInput: function() {
      this.fetchData();
      this.fetchForecast();
      this.recieved = true;
      this.input = "";
    },

    fetchForecast: async function() {
      const res = await fetch(
        `https://api.weatherapi.com/v1/forecast.json?key=484a4fd44fb94f04b3a191835200108&q=${this.input}&days=5`
      );
      const forecast = await res.json();
      this.forecast = forecast.forecast.forecastday;
    }
  }
};
</script>

<style lang="css" scoped>
main {
  background-image: linear-gradient(
    to bottom,
    rgba(0, 0, 0, 0.25),
    rgba(0, 0, 0, 0.75)
  );
  height: 100vh;
  padding: 20px;
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
  display: inline-block;
  padding: 10px 25px;
  color: #fefefe;
  font-size: 102px;
  font-weight: 900;
  text-shadow: 3px 6px rgba(0, 0, 0, 0.25);
}
.dataWrap {
  display: inline-flex;
  flex-direction: row;
  padding: 10px 0px;
  justify-content: center;
  height: auto;
  width: 100%;
}
.icon {
  width: 60px;
  height: 60px;
}
form {
  width: 100%;
  margin-bottom: 30px;
  justify-content: center;
  display: flex;
}
input {
  width: 20vmax;
  display: block;
  padding: 15px;
  color: #313131;
  font-size: 20px;
  border: none;
  appearance: none;
  outline: none;
  background: none;
  background-color: rgba(255, 255, 255, 0.25);
  border-radius: 0px 16px 0px 16px;
  transition: 0.4s;
  box-shadow: 0px 0px 8px rgba(0, 0, 0, 0.25);
}
input::placeholder {
  color: rgba(94, 83, 83, 0.79);
  font-family: "Questrial", sans-serif;
}
input:focus,
input:active {
  width: 60vw;
  outline: none;
  box-shadow: none;
  border-color: transparent;
  background: none;
  appearance: none;
  background-color: rgba(255, 255, 255, 0.75);
  box-shadow: 0px 0px 16px rgba(0, 0, 0, 0.25);
  border-radius: 16px 0px 16px 0px;
  color: rgba(0, 0, 0, 0.75);
}
.currentWrapper {
  background-color: rgba(0, 0, 0, 0.5);
  border-radius: 16px;
  margin: 30px 0;
  box-shadow: 3px 6px 10px rgba(0, 0, 0, 0.25);
  padding: 20px 40px;
  color: #fefefe;
}

.forecastWrap {
  display: flex;
  justify-content: center;
  align-items: flex-start;
  flex-wrap: wrap;
}
.forecastWrap .day {
  background: rgba(0, 0, 0, 0.5);
  padding: 10px;
  margin: 5px;
  color: white;
  width: 200px;
  border-radius: 10px;
}
.hiTemp {
  font-weight: bold;
}
</style>
<template>
  <div>
    <form @submit.prevent="processInput">
      <input type="text" v-model="input" name="input" placeholder="Enter a location" />
    </form>
    <FiveDay />
    <div class="locDate">
      <h1 v-if="recieved" class="location">
        {{location.
        name}},{{location.region}}
      </h1>
    </div>
    <div v-if="recieved" class="currentWrapper">
      <h3 v-if="recieved" class="temp">{{weather.temp_f}}&deg;</h3>

      <div class="dataWrap">
        <h3 v-if="recieved">{{current.text}}</h3>
        <div :style="styles" class="icon"></div>
      </div>
      <h3 v-if="recieved" class="feels">Feels Like {{weather.feelslike_f}}&deg; F</h3>
      <h3 v-if="recieved" class="feels">Humidity {{weather.humidity}}%</h3>
      <h4 v-if="recieved" class="feels">Wind speed {{weather.wind_mph}} Mph</h4>
    </div>
  </div>
</template>

<script>
import FiveDay from "../components/FiveDay";
export default {
  name: "Dashboard",

  data() {
    return {
      location: {},
      weather: {},
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
      this.recieved = true;
      this.input = "";
    }
  },
  components: {
    FiveDay
  }
};
</script>

<style lang="css" scoped>
.locDate {
  position: absolute;
  left: 25px;
  bottom: 80%;
}
.feels,
.time {
  text-align: left;
  margin: 5px;
}
.location {
  font-size: 3vh;
}
.temp {
  font-size: 20vmin;
  margin: 0;
}
.dataWrap {
  display: flex;
  flex-direction: row;
  margin: 0;
  justify-content: center;
}
.icon {
  width: 60px;
}
form {
  position: fixed;
  top: 40px;
  left: 25px;
}
input {
  background: transparent;
  border: none;
  font-size: 4vh;
  color: black;
}
input::placeholder {
  color: rgba(245, 245, 245, 0.575);
  font-family: "Questrial", sans-serif;
}
input:focus,
input:active {
  outline-style: none;
  box-shadow: none;
  border-color: transparent;
  background: transparent;
}
.currentWrapper {
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  flex-wrap: wrap;
  background: rgba(0, 0, 0, 0.5);
  width: 50vmin;
  height: auto;
  position: absolute;
  bottom: 25px;
  left: 25px;
  box-shadow: 0 0 32px rgba(0, 0, 0, 0.5);
  border-radius: 5px;
  padding: 20px;
  overflow: hidden;
  color: white;
}
</style>
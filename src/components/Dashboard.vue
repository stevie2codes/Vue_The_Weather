<template>
  <div>
    <form @submit.prevent="processInput">
      <input type="text" v-model="input" name="input" placeholder="Enter a location" />
    </form>

    <div v-if="recieved" class="currentWrapper">
      <h1 v-if="recieved" class="location">
        {{location.
        name}},{{location.region}}
      </h1>
      <h3 v-if="recieved" class="temp">{{weather.temp_f}}&deg; F</h3>
      <h3 v-if="recieved">Feels Like {{weather.feelslike_f}}&deg; F</h3>
      <div class="dataWrap">
        <h3 v-if="recieved">{{weather.condition.text}}</h3>
        <div :style="styles" class="icon"></div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "Dashboard",

  data() {
    return {
      location: {},
      weather: {},
      input: "",
      recieved: false
    };
  },
  computed: {
    styles() {
      return {
        background: `url(${this.weather.condition.icon})`
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
      } catch (e) {
        console.log(e);
      }
    },
    processInput: function() {
      this.fetchData();
      this.recieved = true;
      this.input = "";
    }
  }
};
</script>

<style lang="css" scoped>
.location {
  font-size: 3vh;
}
.temp {
  font-size: 5vh;
  margin: 0;
}
.dataWrap {
  display: flex;
  flex-direction: row;
  justify-content: center;
}
.icon {
  width: 60px;
  height: 60px;
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
  background: rgba(0, 0, 0, 0.5);
  width: 60%;
  height: 40%;
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  box-shadow: 0 0 32px rgba(0, 0, 0, 0.5);
  border-radius: 5px;
  padding: 20px;
  overflow: hidden;
  color: white;
}
</style>
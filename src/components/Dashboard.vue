<template>
  <div>
    <form @submit.prevent="processInput">
      <input type="text" v-model="input" name="input" placeholder="Enter a location" />
      <button type="submit"></button>
    </form>
    <h1 v-hide="recived ">Enter a Location</h1>
    <div v-show="recieved" class="currentWrapper">
      <h1 v-if="recieved">{{currentWeather.location.name}},{{currentWeather.location.region}}</h1>
      <h3 v-if="recieved">{{currentWeather.current.temp_f}}degrees</h3>
    </div>
  </div>
</template>

<script>
export default {
  name: "Dashboard",

  data() {
    return {
      currentWeather: {},
      input: "",
      recieved: false
    };
  },

  methods: {
    fetchData: async function() {
      try {
        const res = await fetch(
          `http://api.weatherapi.com/v1/current.json?key=484a4fd44fb94f04b3a191835200108&q=${this.input}`
        );
        const currentWeather = await res.json();
        this.currentWeather = currentWeather;
      } catch (e) {
        console.log(e);
      }
    },
    processInput: function() {
      this.fetchData();
      this.recieved = true;
    }
  }
};
</script>

<style lang="css" scoped>
.currentWrapper {
  background-color: white;
  width: 500px;
  margin: auto;
  height: 600px;
  opacity: 0.8;
  border-radius: 10px;
}
</style>
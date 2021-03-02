<template>
  <div class="home">
    <h1 id="home-header">Simple Weather App</h1>
    <form class="form" @submit.prevent="fetchWeather">
      <input
        type="text"
        name="city"
        placeholder="Search for a city"
        v-model.lazy.trim="city"
      />
      <button>SUBMIT</button>
    </form>
    <div v-if="showWeatherBox" class="weather-box">
      <h3>{{city.toUpperCase()}}<sup id="country">{{country}}</sup></h3>
      <h1>{{temp}}&#8451;<!--<sup id="temp-one">o</sup><sup id="temp-two">C</sup>--></h1>
      <div class="image">
        <img alt="image" :src="require(`../assets/${imageUrl}.svg`)" />
      </div>
      <h3 id="last">{{description}}</h3>
    </div>
  </div>
</template>

<script>
// @ is an alias to /src

export default {
  name: "Home",
  data() {
    return {
      weatherDetails: {},
      weatherId: null,
      imageUrl: "clear-sky",
      city: null,
      country: null,
      temp: null,
      description: null,
      showWeatherBox: false
    };
  },
  components: {},
  watch: {
    weatherId: {
      deep: true,
      handler(value) {
        if (value >= 200 && value <= 531) {
          this.imageUrl = "rainy-sky";
        } else if (value >= 600 && value <= 622) {
          this.imageUrl = "snowy-sky";
        } else if (value > 700 && value < 782) {
          this.imageUrl = "hazy-sky";
        } else if (value == 800) {
          this.imageUrl = "clear-sky";
        } else if (value > 800) {
          this.imageUrl = "cloudy-sky";
        }
      },
    },
  },
  methods: {
    fetchWeather() {
      this.showWeatherBox = false
      this.weatherId = 0
      const city = this.city;
      const apiURL = `http://api.openweathermap.org/data/2.5/weather?q=${city}
      &&units=metric&appid=b04c8eb00c085a08275dab2e408b745d`;
      fetch(apiURL)
        .then((response) => response.json())
        .then((data) => {
          console.log(data);
          this.weatherDetails = data;
          this.weatherId = this.weatherDetails.weather[0].id;
          this.description = (this.weatherDetails.weather[0].main).toUpperCase();
          this.country = this.weatherDetails.sys.country
          this.temp = this.weatherDetails.main.temp
          console.log(this.weatherId);
        })
        .catch((error) => alert('Please enter a valid city'))
        .finally(() => {
        if(this.weatherId) {
          this.showWeatherBox = true
        }
        });
    },
  },
};
</script>

<style scoped>
.home {
  margin-top: -18px;
}
#home-header {
  color: white;
  font-size: 55px;
}
.form {
  margin-bottom: 35px;
}
input[type="text"] {
  border: none;
  border-bottom: 2px solid white;
  background: #0b1f44;
  color: white;
  font-size: 35px;
  width: 330px;
  margin-right: 10px;
  box-sizing: border-box;
}
::placeholder {
  color: white;
}
button {
  width: 90px;
  height: 45px;
  /* padding: 16px 25px; */
  background: #ff1f43;
  color: white;
  border: none;
  border-radius: 5px;
  font-weight: bold;
  font-size: 15px;
}
.weather-box {
  max-width: 250px;
  max-height: 400px;
  background: white;
  display: flex;
  flex-direction: column;
  text-align: left;
  margin: 0 auto;
  padding: 15px 15px 15px 30px;
  border: none;
  border-radius: 12px;
  margin-bottom: 20px;
}
.weather-box h3 {
  color: #52627b;
  font-weight: bold;
  font-size: 25px;
}
#country {
  color: #ff8c02;
  font-weight: bold;
  margin-left: 5px;
  font-size: 12px;
}
/* #temp-one {
  position: relative;
  font-weight: bold;
  margin-left: 5px;
  font-size: 35px;
}
#temp-two {
  position: relative;
  font-weight: bold;
  margin-left: 5px;
  font-size: 35px;
} */
.weather-box h1 {
  color: black;
  font-size: 55px;
  font-weight: bold;
  margin-top: -5px;
}
.image {
  margin-top: -35px;
  margin-bottom: 15px;
}
.image img {
  max-width: 150px;
  max-height: 150px;
}
#last {
  margin-top: -10px
}
</style>

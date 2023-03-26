<template>
  <div v-for="city in savedCities" key="city.id">
    <CityCard :city="city" @click="goToCityView(city)"></CityCard>
  </div>
  <p v-if="savedCities.length === 0">
没有添加城市，请输入并选择城市。
  </p>
</template>

<script setup>
import axios from "axios";
import { ref } from "vue";
import CityCard from "./CityCard.vue";
import { useRouter } from "vue-router";
// let weatherData = ref([]);
const savedCities = ref([]);
const getCities = async () => {
  if (localStorage.getItem("savedCities")) {
    savedCities.value = JSON.parse(localStorage.getItem("savedCities"));
    const request = [];
   savedCities.value.forEach(async (city,index) => {
      const adcode = await axios.get(
        `https://restapi.amap.com/v3/geocode/regeo?location=${city.location}&key=ba566aa3a8ca0e6c05890d0fefc27d5a&radius=1000&extensions=base`
      );
      const adcodeResult =adcode.data.regeocode.addressComponent.adcode;
      
      request.push(
        axios.get(
          `https://restapi.amap.com/v3/weather/weatherInfo?city=${adcodeResult}&key=5fd1f06e35413a4ec03d2569ee7857b8&extensions=all`
        )
      );
    });
    const weatherData = await Promise.all(request)
      weatherData.forEach((value, index) => {
        savedCities.value[index].weather = value.data;
      });
  }
};
await getCities();
const router = useRouter();
const goToCityView = (city) => {
  router.push({
    name: "cityView",
    params: { state: city.state, city: city.city },
    query: {
      id: city.id,
      location:city.location
    },
  });
};
</script>

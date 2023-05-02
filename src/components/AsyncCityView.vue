<template>
  <div class="flex flex-col flex-1 items-center">
    <div
      v-if="route.query.preview"
      class="text-white p-4 bg-weather-secondary w-full text-center"
    >
      <p>该城市的天气预报如下，点击+号开始保存该城市</p>
    </div>
    <div class="flex flex-col items-center text-white py-12">
      <h1 class="text-4xl mb-2">
        {{ weather.data.forecasts[0].province }} {{ route.params.city }}
        {{ weather.data.forecasts[0].city }}
      </h1>
      <p class="text-sm mb-12">
        {{ weather.data.forecasts[0].reporttime.split(" ")[0] }} 星期{{
          weather.data.forecasts[0].casts[0].week
        }}
      </p>
      <p class="text-8xl mb-8">
        {{ weather.data.forecasts[0].casts[0].daytemp_float }}°
      </p>
      <div class="text-center">
        <p>体感温度：{{ weather.data.forecasts[0].casts[0].daytemp }}°</p>
        <p class="capitalize">
          {{ weather.data.forecasts[0].casts[0].dayweather }}
          {{ weather.data.forecasts[0].casts[0].daywind }}风
        </p>
        <p></p>
      </div>
    </div>
    <hr class="border-white border-opacity-10 border w-full" />
    <div class="max-w-screen-md w-full py-12">
      <div class="mx-8 text-white">
        <h2 class="mb-4">天气预报</h2>
        <div class="flex gap-40">
          <div
            v-for="dayData in weather.data.forecasts[0].casts"
            class="flex flex-col gap-4 items-center"
          >
            <p class="whitespace-nowrap text-md">
              {{ dayData.date.split("-")[1] }}-{{ dayData.date.split("-")[2] }}
            </p>
            <p class="whitespace-nowrap text-md">
              {{ dayData.dayweather }}
              {{ weather.data.forecasts[0].casts[0].daywind }}风
            </p>
            <p class="whitespace-nowrap text-md">
              {{ dayData.daytemp_float }}°
            </p>
          </div>
        </div>
      </div>
    </div>
    <div class="flex items-center gap-2 py-12 text-white  cursor-pointer duration-150 hover:text-red-500" @click="removeCity">
    <i class="fa-solid fa-trash"></i>
    <p>删除该城市</p>
      
    </div>
  </div>
</template>

<script setup>
import axios from "axios";
import { useRoute,useRouter} from "vue-router";
import { ref } from "vue";
const route = useRoute();

const getAdcodeKey = ref("ba566aa3a8ca0e6c05890d0fefc27d5a");
const getWeatherKey = ref("5fd1f06e35413a4ec03d2569ee7857b8");
const getAdcode = async () => {
  try {
    const AdcodeResult = await axios.get(
      `https://restapi.amap.com/v3/geocode/regeo?location=${route.query.location}&key=${getAdcodeKey.value}&radius=1000&extensions=base`
    );

    return AdcodeResult.data.regeocode.addressComponent.adcode;
  } catch (err) {
    console.log(err);
  }
};
const adcode = await getAdcode();
const getWeather = async () => {
  try {
    const weatherData = await axios.get(
      `https://restapi.amap.com/v3/weather/weatherInfo?city=${adcode}&key=${getWeatherKey.value}&extensions=all`
    );

    await new Promise((res)=>setTimeout(res,1000))
    return weatherData;
  } catch (error) {
    console.log(error);
  }
};
const weather = await getWeather();
console.log(weather.data.forecasts[0].reporttime.split(" ")[0]);
const router = useRouter()
const removeCity =()=>{

  const cities = JSON.parse(localStorage.getItem("savedCities"))
  const updatedCities = cities.filter(city=>
    city.id !==route.query.id
  )
  localStorage.setItem('savedCities',JSON.stringify(updatedCities))
  router.push({
    name:'home'
  })
}
</script>


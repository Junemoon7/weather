
<template>
  <main class="container text-white">
    <div class="pt-4 mb-8 relative">
      <input
        v-model="searchQuery"
        @input="getSearchResult"
        type="text"
        placeholder="请输入要查询的省份"
        class="py-2 px-1 w-full bg-transparent border-b focus: border-weather-secondary focus:outline-none focus:shadow-[0px_1px_0_0_#004E71]"
      />
      <ul
        class="absolute bg-weather-secondary text-white w-full shadow-md py-2 px-1 top-[66px ]"
        v-if="mapboxSearchResults"
      >
        <p v-if="serverError">对不起，服务器正在繁忙请稍后重试。</p>
        <p v-if="!serverError && mapboxSearchResults.length === 0">
          没有找到您所输入的城市
        </p>
        <li
          @click="previewCity(searchResult)"
          v-else
          v-for="searchResult in mapboxSearchResults"
          ::key="searchResult.id"
          class="py-2 cursor-pointer"
        >
          {{ searchResult.place_name }}
        </li>
      </ul>
    </div>
    <div class="flex flex-col gap-4">
      <Suspense>
        <CityList></CityList>
        <template #fallback>
          <CityCardSkeleton></CityCardSkeleton>
        </template>
      </Suspense>
    </div>
  </main>
</template>
<script setup>
import { ref } from "vue";
import { useRouter } from "vue-router";
import axios from "axios";
import CityList from "../components/CityList.vue";
import CityViewVue from "./CityView.vue";
import CityCardSkeleton from "../components/CityCardSkeleton.vue";
const router = useRouter();
const searchQuery = ref("");
const mapboxAPIKey =
  "pk.eyJ1IjoianVuZW1vb24iLCJhIjoiY2xmbTNzNXN4MDgxcTQxbzkwdTRiZ3dzayJ9.irsYvJOjFn4TC33CyKO_yQ";
const mapboxSearchResults = ref(null);
const queryTimeout = ref(null);
const serverError = ref(null);
const previewCity = (searchResult) => {
  const [city, state] = searchResult.place_name.split(",");
  console.log(city + state);
  router.push({
    name: "cityView",
    params: { city: city.replaceAll(" ", ""), state: state },
    query: {
      location: `${searchResult.geometry.coordinates[0]},${searchResult.geometry.coordinates[1]}`,
      preview: true,
    },
  });
};
const getSearchResult = () => {
  try {
    clearTimeout(queryTimeout.value);
    queryTimeout.value = setTimeout(async () => {
      if (searchQuery.value !== "") {
        const result = await axios.get(
          `https://api.mapbox.com/geocoding/v5/mapbox.places/${searchQuery.value}.json?access_token=${mapboxAPIKey}&types=place`
        );
        mapboxSearchResults.value = result.data.features;
        console.log(result);
        return;
      }
      mapboxSearchResults.value = null;
    }, 300);
  } catch (error) {
    serverError = true;
  }
};
</script>

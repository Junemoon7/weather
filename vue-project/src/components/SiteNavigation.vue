<template>
  <header class="sticky top-0 bg-weather-primary shadow-lg">
    <nav
      class="container flex flex-col sm:flex-row items-center gap-3 text-white py-6"
    >
      <RouterLink :to="{ name: 'home' }">
        <div class="flex items-center gap-3">
          <i class="fas fa-sun text-2xl" />
          <p class="text-2xl">本地天气</p>
        </div>
      </RouterLink>
      <div class="flex gap-3 flex-1 justify-end">
        <i
          @click="toggleModel"
          class="fa-solid fa-circle-info text-xl hover:text-weather-secondary duration-150 cursor-pointer"
        />
        <i
          class="fas fa-plus text-xl hover:text-weather-secondary duration-150 cursor-pointer"
          @click="addCity"
          v-if="route.query.preview"
        />
      </div>
      <BaseModel @close-model="toggleModel" :modelActive="modelActive"
        ><h1 class="text-black">
          <div class="text-black">
            <h1 class="text-2xl mb-1">关于</h1>
            <p class="mb-4">
              该系统允许您查询当前和未来您选择的城市的天气。通过在搜索栏中输入来搜索您的城市。
            </p>
            <h2 class="text-2xl">如何工作</h2>
            <ol class="list-decimal list-inside mb-4">
              <li>
                在下拉框中选择一个城市，这可以查看当前城市的天气。
              </li>
              <li>
                点击右上角的“+”图标，追踪城市。这将保存城市，以便以后查看
              </li>
             
            </ol>

            <h2 class="text-2xl">删除城市</h2>
            <p>
              如果您不再希望跟踪某个城市，只需选择该城市

在主页中。在页面的底部，会有一个

删除城市的选项.
            </p>
          </div>
        </h1></BaseModel
      >
    </nav>
  </header>
</template>

<script setup>
import { RouterLink, useRoute, useRouter } from "vue-router";
import BaseModel from "./BaseModel.vue";
import { ref } from "vue";
import { uid } from "uid";
const modelActive = ref(null);
const route = useRoute();
const router = useRouter();
const savedCities = ref([]);
const toggleModel = () => {
  modelActive.value = !modelActive.value;
};
const addCity = () => {
  if (localStorage.getItem("savedCities")) {
    savedCities.value = JSON.parse(localStorage.getItem("savedCities"));
  }
  const locationObj = {
    id: uid(),
    state: route.params.state,
    city: route.params.city,
    location: route.query.location,
    weather: Object,
  };

  savedCities.value.push(locationObj);
  localStorage.setItem("savedCities", JSON.stringify(savedCities.value));
  let query = Object.assign({}, route.query);
  delete query.preview;
  query.id = locationObj.id
  router.replace({ query });
};
</script>

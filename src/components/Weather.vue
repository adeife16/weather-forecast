<template>
  <div class="p-4 bg-purple-100 weather-screen">
    <!-- Header -->
    <div class="flex justify-between items-center">
      <div>
        <p class="text-lg font-semibold">{{ city }}</p>
        <p class="text-5xl font-bold mt-2">{{ temperature }}°</p>
        <p class="text-sm">{{ day }}</p>
      </div>
      <button class="text-gray-500">
        <MagnifyingGlassIcon class="h-6 w-6" />
      </button>
    </div>

    <!-- Tabs
    <div class="flex space-x-4 mt-6">
      <button class="px-4 py-2 bg-white rounded-full shadow-md">Today</button>
      <button class="px-4 py-2 bg-white rounded-full shadow-md">
        Tomorrow
      </button>
      <button class="px-4 py-2 bg-purple-300 rounded-full shadow-md">
        10 days
      </button>
    </div> -->

    <!-- Forecast List -->
    <div class="mt-6 space-y-4">
      <!-- Daily Forecast Card -->
      <div
        class="p-4 bg-purple-200 rounded-lg flex justify-between items-center"
        v-for="day in otherDays"
        :key="day.datetime"
      >
        <div>
          <p class="text-sm font-semibold">{{ day.day }}</p>
          <p class="text-xs">{{ day.weather.description }}</p>
        </div>
        <div class="flex items-center space-x-2">
          <p class="text-lg font-semibold">{{ day.temp }}°</p>
          <img
            :src="`https://cdn.weatherbit.io/static/img/icons/${day.weather.icon}.png`"
            alt="Weather Icon"
            class="w-10 h-10"
          />
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, watch } from "vue";

const userLocation = ref(null);
const apiKey = process.env.VUE_APP_API_KEY;
const city = ref("");
const temperature = ref("");
const day = ref("");
const today = ref([]);
const otherDays = ref([]);

onMounted(() => {
  if (navigator.geolocation) {
    navigator.geolocation.getCurrentPosition(
      (position) => {
        userLocation.value = {
          latitude: position.coords.latitude,
          longitude: position.coords.longitude,
        };
      },
      (error) => {
        console.error("Error getting location:", error);
      }
    );
  } else {
    console.error("Geolocation is not supported by this browser.");
  }
});

const dayOfWeek = (dateStr) => {
  const date = new Date(dateStr);

  const options = { weekday: "long", month: "short", day: "numeric" };
  const formattedDate = date.toLocaleDateString("en-US", options);
  console.log(formattedDate);
  return formattedDate;
};

const displayForecast = (data) => {
  city.value = data.city_name;
  temperature.value = data.data[0].temp;
  day.value = dayOfWeek(data.data[0].datetime);
  otherDays.value = data.data.slice(1);

  let arr = [];

  otherDays.value.forEach((day) => {
    arr.push({
      datetime: day.datetime,
      temp: day.temp,
      day: dayOfWeek(day.datetime),
      weather: day.weather,
    });
  });
  otherDays.value = arr;
};

watch(userLocation, (newLocation) => {
  if (newLocation) {
    fetch(
      `https://api.weatherbit.io/v2.0/forecast/daily?lat=${newLocation.latitude}&lon=${newLocation.longitude}&key=${apiKey}`
    )
      .then((response) => response.json())
      .then((data) => {
        console.log(data);

        displayForecast(data);
      })
      .catch((error) => console.error("Fetch error:", error));
  }
});
</script>

<style scoped>
.weather-screen {
  height: 90%;
}
</style>
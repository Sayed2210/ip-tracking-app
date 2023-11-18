<template>
  <div class="home flex flex-col h-screen max-h-screen">
    <!-- search / result -->
    <div
      class="input z-20 flex justify-center bg-cover px-4 pt-8 pb-32 bg-hero-pattern relative"
    >
      <!-- search input -->
      <div class="w-full max-w-screen-sm">
        <h1 class="text-center text-white text-3xl pb-4">IP Address Tracker</h1>
        <div class="flex">
          <input
            type="text"
            v-model="queryIp"
            name="search"
            class="flex-1 py-3 px-2 rounded-tl-md rounded-bl-md focus:outline-none"
            placeholder="Search for any IP address or leave empty to get your ip info"
          />
          <font-awesome-icon
            @click="getIpInfo"
            icon="fa-solid fa-arrow-right"
            class="cursor-pointer py-5 border border-black self-stretch bg-black text-white px-4 rounded-tr-md rounded-br-md flex items-center"
          />
        </div>
      </div>
      <!-- results -->
      <IPInfo v-if="ipInfo" :ipInfo="ipInfo" />
    </div>
    <!-- Map -->
    <div id="mapid" class="h-full z-10"></div>
  </div>
</template>

<script setup>
import IPInfo from "@/components/IPInfo.vue";
import { ref, onMounted } from "vue";
import leaflet from "leaflet";
import axios from "axios";
let mymap = ref();

// data
const queryIp = ref("");
const ipInfo = ref(null);
// mounted lifecycle hook, creates the map
onMounted(() => {
  mymap = leaflet.map("mapid").setView([42.5145, -83.0147], 9);

  leaflet
    .tileLayer("https://tile.openstreetmap.org/{z}/{x}/{y}.png", {
      attribution:
        'Map data &copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors, Imagery © <a href="https://www.mapbox.com/">Mapbox</a>',
      maxZoom: 18,
      id: "mapbox/streets-v11",
      tileSize: 512,
      zoomOffset: -1,
      accessToken:
        "pk.eyJ1Ijoiam9obmtvbWFybmlja2kiLCJhIjoiY2txcGJ3bmoyMHRjMzJ2cGNpMnlsc3pqeiJ9.mSoT7QxEgjVEmNRN9k_NKg",
    })
    .addTo(mymap);
});
const getIpInfo = async () => {
  try {
    const data = await axios.get(
      `https://geo.ipify.org/api/v2/country?apiKey=at_fxkQruCKFWte6vUmohaoTzhREjEzS&ipAddress=${queryIp.value}`
    );
    const result = data.data;
    ipInfo.value = {
      address: result.ip,
      state: result.location.region,
      timezone: result.location.timezone,
      isp: result.isp,
    };
    console.log(ipInfo.value);
    // leaflet.marker([ipInfo.value.lat, ipInfo.value.lng]).addTo(mymap);
    // mymap.value.setView([ipInfo.value.lat, ipInfo.value.lng], 13);
  } catch (err) {
    alert(err.message);
  }
};
</script>

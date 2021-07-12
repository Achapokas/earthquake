<template>
  <v-app>
    <side-bar
      :start-time.sync="startTime"
      :end-time.sync="endTime"
      :min-magnitude.sync="minMagnitude"
    />
    <v-main>
      <v-container fluid fill-height d-flex pa-0>
        <main-map
          class="flex-grow-1 fill-height"
          :start-time="startTime"
          :end-time="endTime"
          :min-magnitude="minMagnitude"
        />
      </v-container>
    </v-main>
  </v-app>
</template>

<script>
import MainMap from "./components/Map";
import SideBar from "./components/SideBar";

console.log(Date.now());

export default {
  name: "App",
  components: {
    SideBar,
    MainMap,
  },

  data() {
    return {
      startTime: "2021-07-11",
      endTime: "2021-07-14",
      minMagnitude: 0,
    };
  },
};
</script>

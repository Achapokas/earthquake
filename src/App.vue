<template>
  <v-app>
    <side-bar
      :start-time.sync="fields.startTime"
      :end-time.sync="fields.endTime"
      :min-magnitude.sync="fields.minMagnitude"
    />
    <v-main>
      <v-alert class="m-0" v-if="this.error" dense type="error">{{
        this.error
      }}</v-alert>
      <v-container fluid fill-height d-flex pa-0>
        <main-map
          class="flex-grow-1 fill-height"
          :fields="fields"
          :error.sync="error"
        />
      </v-container>
    </v-main>
  </v-app>
</template>

<script>
import MainMap from "./components/Map";
import SideBar from "./components/SideBar";

export default {
  name: "App",
  components: {
    SideBar,
    MainMap,
  },

  data() {
    return {
      error: "",
      fields: {
        startTime: "2021-07-11",
        endTime: "2021-07-14",
        minMagnitude: 0,
      },
    };
  },
};
</script>

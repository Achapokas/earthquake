<template>
  <mapbox-map
    access-token="pk.eyJ1IjoibWF1cmltaXJhbmRhIiwiYSI6ImNqc3FsZ2JpaDE5OWI0NHA2dDI5aG5vdTcifQ.dQsXVmW2MgVsj0FhDrSeQA"
    map-style="mapbox://styles/mapbox/light-v10"
    :center="[0, 0]"
    :zoom="2"
    @mb-created="(mapInstance) => (map = mapInstance)"
  >
    <mapbox-source id="usgs" :options="sourceOptions" />
    <mapbox-layer id="earthquakes" :options="layerOptions" />
  </mapbox-map>
</template>

<script>
import axios from "axios";
let radius = 8;

export default {
  name: "Map",
  props: { startTime: String, endTime: String, minMagnitude: Number },
  mounted() {
    axios
      .get(
        `https://earthquake.usgs.gov/fdsnws/event/1/query?format=geojson&starttime=${this.startTime}&endtime=${this.endTime}&minmagnitude=${this.minMagnitude}`
      )
      .then((response) => (this.sourceOptions.data = response.data))
      .catch((error) => (this.error = error));
  },
  watch: {
    startTime: function (newStartTime) {
      axios
        .get(
          `https://earthquake.usgs.gov/fdsnws/event/1/query?format=geojson&starttime=${newStartTime}&endtime=${this.endTime}&minmagnitude=${this.minMagnitude}`
        )
        .then((response) => (this.sourceOptions.data = response.data))
        .catch((error) => (this.error = error));
    },
  },
  data() {
    return {
      map: null,
      error: "",
      sourceOptions: {
        type: "geojson",
        data: {},
      },
      layerOptions: {
        type: "circle",
        source: "usgs",
        paint: {
          "circle-radius": radius,
          "circle-stroke-width": 1,
          "circle-color": "red",
          "circle-stroke-color": "white",
        },
      },
    };
  },
};
</script>

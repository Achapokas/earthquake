<template>
  <mapbox-map
    access-token="pk.eyJ1IjoibWF1cmltaXJhbmRhIiwiYSI6ImNqc3FsZ2JpaDE5OWI0NHA2dDI5aG5vdTcifQ.dQsXVmW2MgVsj0FhDrSeQA"
    map-style="mapbox://styles/mapbox/light-v10"
    :center="[0, 0]"
    :zoom="2"
    @mb-created="(mapInstance) => (map = mapInstance)"
  >
    <mapbox-popup
      :lng-lat="popup.position"
      v-if="popup.isOpen"
      @mb-close="() => (popup.isOpen = false)"
    >
      <ul>
        <li v-if="popup.data.place">Location: {{ popup.data.place }}</li>
        <li v-if="popup.data.time">Date: {{ popup.data.time }}</li>
        <li v-if="popup.data.mag">Magnitude: {{ popup.data.mag }}</li>
      </ul>
    </mapbox-popup>
    <mapbox-source id="usgs" :options="sourceOptions" />
    <mapbox-layer id="earthquakes" :options="layerOptions" />
  </mapbox-map>
</template>

<script>
import axios from "axios";

export default {
  name: "Map",
  props: {
    fields: Object,
  },
  methods: {
    request(start, end, mag) {
      axios
        .get(
          `https://earthquake.usgs.gov/fdsnws/event/1/query?format=geojson&starttime=${start}&endtime=${end}&minmagnitude=${mag}`
        )
        .then((response) => (this.sourceOptions.data = response.data))
        .catch((error) => this.$emit("update:error", error));
    },
    createPopup(popup) {
      this.map.on("click", "earthquakes", function (e) {
        popup.position = [e.lngLat.lng, e.lngLat.lat];
        popup.isOpen = true;

        popup.data = e.features[0].properties;
      });
    },
  },
  mounted() {
    this.$emit("update:error", "");
    this.request(
      this.fields.startTime,
      this.fields.endTime,
      this.fields.minMagnitude
    );

    this.createPopup(this.popup);
  },
  watch: {
    fields: {
      handler(newFields) {
        this.request(
          newFields.startTime,
          newFields.endTime,
          newFields.minMagnitude
        );
      },
      deep: true,
    },
  },
  data() {
    const magnitude = {
      mag1: ["all", [">", ["get", "mag"], 0], ["<", ["get", "mag"], 1]],
      mag2: ["all", [">", ["get", "mag"], 1], ["<", ["get", "mag"], 2]],
      mag3: ["all", [">", ["get", "mag"], 2], ["<", ["get", "mag"], 3]],
      mag4: ["all", [">=", ["get", "mag"], 4], ["<", ["get", "mag"], 5]],
      mag5: [">=", ["get", "mag"], 5],
    };

    const colors = ["#fed976", "#feb24c", "#fd8d3c", "#fc4e2a", "#e31a1c"];

    return {
      map: null,
      popup: {
        isOpen: false,
        data: null,
        position: [0, 0],
      },
      sourceOptions: {
        type: "geojson",
        data: {},
      },
      layerOptions: {
        type: "circle",
        source: "usgs",
        paint: {
          "circle-radius": [
            "case",
            magnitude.mag1,
            1,
            magnitude.mag2,
            2,
            magnitude.mag3,
            3,
            magnitude.mag4,
            4,
            magnitude.mag5,
            5,
            0,
          ],
          "circle-stroke-width": 1,
          "circle-color": [
            "case",
            magnitude.mag1,
            colors[0],
            magnitude.mag2,
            colors[1],
            magnitude.mag3,
            colors[2],
            magnitude.mag4,
            colors[3],
            colors[4],
          ],
          "circle-stroke-color": "white",
        },
      },
    };
  },
};
</script>

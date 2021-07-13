<template>
  <v-navigation-drawer app dark permanent width="350">
    <v-app-bar dark>
      <v-toolbar-title>Earthquakes Map</v-toolbar-title>
    </v-app-bar>
    <div class="ma-4">
      <v-text-field
        label="Start time"
        type="date"
        :value="startTime"
        @input="$emit('update:startTime', $event)"
      ></v-text-field>
      <v-text-field
        label="End time"
        type="date"
        :value="endTime"
        @input="$emit('update:endTime', $event)"
      ></v-text-field>
      <v-text-field
        label="Min Magnitude"
        type="number"
        :value="minMagnitude"
        @input="$emit('update:minMagnitude', Number($event))"
      ></v-text-field>
    </div>
  </v-navigation-drawer>
</template>

<script>
export default {
  name: "SideBar",
  props: { startTime: String, endTime: String, minMagnitude: Number },
};
</script>

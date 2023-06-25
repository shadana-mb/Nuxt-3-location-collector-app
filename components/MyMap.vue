<template>
  <ol-map
    :loadTilesWhileAnimating="true"
    :loadTilesWhileInteracting="true"
    style="height: 400px"
    ref="map"
    @click="handleClick"
  >
    <ol-view
      ref="view"
      :center="center"
      :rotation="rotation"
      :zoom="zoom"
      :projection="projection"
    />
    <ol-zoom-control />
    <ol-tile-layer>
      <ol-source-osm />
    </ol-tile-layer>

    <ol-geolocation :projection="projection" @positionChanged="geoLocChange">
      <template v-slot="slotProps">
        <ol-vector-layer :zIndex="2">
          <ol-source-vector>
            <ol-feature ref="positionFeature">
              <ol-geom-point :coordinates="slotProps.position"></ol-geom-point>
              <ol-style>
                <ol-style-icon :src="hereIcon" :scale="0.06"></ol-style-icon>
              </ol-style>
            </ol-feature>
          </ol-source-vector>
        </ol-vector-layer>
      </template>
    </ol-geolocation>

    <ol-vector-layer v-if="showMarker" :zIndex="3">
      <ol-source-vector>
        <ol-feature>
          <ol-geom-point :coordinates="markerData.coordinate"></ol-geom-point>
          <ol-style>
            <ol-style-icon :src="thereIcon" :scale="0.05"></ol-style-icon>
          </ol-style>
        </ol-feature>
      </ol-source-vector>
    </ol-vector-layer>
  </ol-map>
  <p v-if="showMarker">
    the coordinates of your selected point is:
    <span
      >lat: {{ markerData.coordinate[0] }} , long:
      {{ markerData.coordinate[1] }}
    </span>
  </p>
</template>

<script setup>
import hereIcon from "~/assets/here.png";
import thereIcon from "~/assets/there.png";
import { ref, reactive, computed } from "vue";

const center = ref([51.3, 35.7]);
const projection = ref("EPSG:4326");
const zoom = ref(8);
const rotation = ref(0);
const view = ref(null);
const map = ref(null);

//set map view to user's location
const geoLocChange = (loc) => {
  console.log(loc);
  view.value.fit([loc[0], loc[1], loc[0], loc[1]], {
    maxZoom: 14,
  });
};

//add marker to the map on click
const markerData = reactive({
  coordinate: null,
});

const handleClick = (event) => {
  markerData.coordinate = event.coordinate;
  console.log("c", markerData.coordinate);
};

const showMarker = computed(() => {
  if (markerData.coordinate === null) return false;
  return true;
});
</script>

<style>
span {
  font-weight: 700;
}
</style>

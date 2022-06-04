<template>
  <div class="map-root">
    <div ref="mapContainerRef" class="map-el" />
    <div ref="markerRef" class="map-marker" />
  </div>
</template>
<script lang="ts" setup>
import Map from 'ol/Map';
import View from 'ol/View';
import TileLayer from 'ol/layer/Tile';
import OSM from 'ol/source/OSM';
import { fromLonLat } from 'ol/proj';
import Overlay from 'ol/Overlay';
import OverlayPositioning from 'ol/OverlayPositioning';
import { ref, onMounted, defineProps, watch } from 'vue';
const props = defineProps({
  lng: { type: Number, default: -71.418884 },
  lat: { type: Number, default: 41.825226 },
  zoom: { type: Number, default: 10 },
});
const mapContainerRef = ref();
const markerRef = ref();
const tileLayer = new TileLayer({
  source: new OSM(),
});
// const markerOverlay = ref<Overlay>();
// const mapInst = ref<Map>();
let map = new Map({
  layers: [tileLayer],
  view: new View({
    center: fromLonLat([props.lng, props.lat]),
    zoom: props.zoom,
  }),
  controls: [],
});
let markerOverlay = new Overlay({
  positioning: OverlayPositioning.CENTER_CENTER,
  stopEvent: false,
});

onMounted(() => {
  map.setTarget(mapContainerRef.value as unknown as HTMLDivElement);
  map.render();

  markerOverlay.setElement(markerRef.value as unknown as HTMLDivElement);
  map.addOverlay(markerOverlay);
  markerOverlay.setPosition(fromLonLat([props.lng, props.lat]));
  map.getView().animate({ center: fromLonLat([props.lng, props.lat]) });
});

watch([() => props.lat, () => props.lng, () => props.zoom], () => {
  markerOverlay.setPosition(fromLonLat([props.lng, props.lat]));
  map.getView().animate({ center: fromLonLat([props.lng, props.lat]) });
});
</script>
<style>
.map-root,
.map-el {
  height: 100%;
  width: 100%;
}
.map-marker {
  width: 22px;
  height: 22px;
  border-radius: 50%;
  background: #66abff;
  border: 3px solid white;
  box-shadow: 0 3px 3px -2px rgba(0, 0, 0, 0.2), 0 3px 4px 0 rgba(0, 0, 0, 0.14),
    0 1px 8px 0 rgba(0, 0, 0, 0.12);
}
</style>

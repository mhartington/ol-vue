<template>
  <ion-page>
    <ion-header :translucent="true">
      <ion-toolbar>
        <ion-title>Blank</ion-title>
        <ion-buttons slot="end">
          <ion-button @click="trackLocation()">Track Location</ion-button>
        </ion-buttons>
      </ion-toolbar>
    </ion-header>

    <ion-content :fullscreen="true">
      <app-map :lat="location.lat" :lng="location.lng" :zoom="location.zoom" />
    </ion-content>
  </ion-page>
</template>

<script lang="ts" setup>
/* eslint-disable @typescript-eslint/no-non-null-assertion */

import AppMap from '../components/AppMap.vue';
import {
  IonContent,
  IonHeader,
  IonPage,
  IonTitle,
  IonToolbar,
  IonButtons,
  IonButton,
} from '@ionic/vue';
import { Geolocation } from '@capacitor/geolocation';
import { Capacitor } from '@capacitor/core';
import { ref, onUnmounted, onMounted } from 'vue';

const trackingRef = ref();
const isLoaded = ref(false);

const location = ref({
  lng: -71.418884,
  lat: 41.825226,
  zoom: 10,
});
onMounted(() => {
  isLoaded.value = true;
  requestPermission();
});
onUnmounted(() => {
  if (trackingRef.value) {
    Geolocation.clearWatch({ id: trackingRef.value });
  }
});
const requestPermission = async () => {
  if (Capacitor.isNativePlatform()) {
    await Geolocation.requestPermissions();
  }
  await getLocation();
};
const getLocation = async () => {
  const loc = await Geolocation.getCurrentPosition();
  location.value = {
    lng: loc.coords.longitude,
    lat: loc.coords.latitude,
    zoom: 12,
  };
};
const trackLocation = async () => {
  if (trackingRef.value !== undefined) {
    trackingRef.value = Geolocation.watchPosition({}, (loc) => {
      location.value = {
        lng: loc!.coords.longitude,
        lat: loc!.coords.latitude,
        zoom: 10,
      };
    });
  }
};
</script>

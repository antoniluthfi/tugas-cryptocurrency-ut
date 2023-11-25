<template>
  <ion-app>
    <ion-header>
      <ion-toolbar>
        <ion-title> Crypto App </ion-title>
      </ion-toolbar>
    </ion-header>

    <ion-content>
      <ion-row class="ion-justify-content-center ion-margin">
        <ion-button @click="getCryptos">Refresh</ion-button>
      </ion-row>

      <ion-list>
        <ion-item v-for="crypto, index in cryptos" :key="crypto.id">
          <ion-label>
            <p class="ion-text-center">Rank</p>
            <h1 class="ion-text-center">{{ index + 1 }}</h1>
          </ion-label>
          <ion-label>
            <p>{{ crypto.name }}</p>
            <h1>{{ crypto.symbol }}</h1>
          </ion-label>
          <ion-label>
            <p>USD</p>
            <h1>{{ crypto.price_usd }}</h1>
          </ion-label>
        </ion-item>
      </ion-list>
    </ion-content>
  </ion-app>
</template>

<script setup lang="ts">
import {
  IonContent,
  IonHeader,
  IonTitle,
  IonToolbar,
  IonApp,
  IonRow,
  IonButton,
  IonItem,
  IonLabel,
  IonList,
  toastController,
  ToastOptions,
} from "@ionic/vue";
import { Crypto } from "./HomePage.interface";
</script>

<script lang="ts">
export default {
  data() {
    return {
      cryptos: [] as Crypto[],
      isOnline: navigator.onLine,
    };
  },
  watch: {
    isOnline(newStatus) {
      if (newStatus) {
        this.showToast("You're online", "success");
      } else {
        this.showToast("No internet connection", "danger");
      }
    },
  },
  mounted() {
    window.addEventListener("online", () => {
      this.isOnline = true;
    });

    window.addEventListener("offline", () => {
      this.isOnline = false;
    });

    this.getCryptos();
  },
  methods: {
    getCryptos() {
      fetch("https://api.coinlore.net/api/tickers/")
        .then((response) => response.json())
        .then((data) => {
          this.cryptos = data.data;
        })
        .catch((error) => {
          console.log(error);
        });
    },
    async showToast(message: string, color: ToastOptions["color"]) {
      const toast = await toastController.create({
        message: message,
        duration: 3000,
        position: "bottom",
        color,
      });
      await toast.present();
    },
  },
};
</script>

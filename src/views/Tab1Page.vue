<template>
  <ion-page>
    <ion-header>
      <ion-toolbar>
        <ion-title>Crypto Prices</ion-title>
      </ion-toolbar>
    </ion-header>
    <ion-content>
      <ion-header collapse="condense">
        <ion-toolbar>
          <ion-title size="large">Crypto Prices</ion-title>
        </ion-toolbar>
      </ion-header>
      <div style="display: flex; justify-content: center; margin-top: 20px">
        <ion-button @click="fetchData">Get Data</ion-button>
      </div>
      <ion-grid>
        <ion-row>
          <ion-col>Name</ion-col>
          <ion-col>Symbol</ion-col>
          <ion-col>Price USD</ion-col>
        </ion-row>
        <ion-row v-for="coin in paginatedCoins" :key="coin.id">
          <ion-col>{{ coin.name }}</ion-col>
          <ion-col>{{ coin.symbol }}</ion-col>
          <ion-col>{{ coin.price_usd }}</ion-col>
        </ion-row>
      </ion-grid>
      <div style="display: flex; justify-content: center; margin-top: 20px">
        <ion-button @click="prevPage" :disabled="currentPage === 1"
          >Previous</ion-button
        >
        <ion-button @click="nextPage" :disabled="currentPage === totalPages"
          >Next</ion-button
        >
      </div>
    </ion-content>
  </ion-page>
</template>

<script setup lang="ts">
import {
  IonPage,
  IonHeader,
  IonToolbar,
  IonTitle,
  IonContent,
  IonButton,
  IonGrid,
  IonRow,
  IonCol,
} from "@ionic/vue";
import { ref, computed } from "vue";
import axios from "axios";

const coins = ref([]);
const currentPage = ref(1);
const itemsPerPage = 10;

const fetchData = async () => {
  try {
    const response = await axios.get("https://api.coinlore.net/api/tickers/");
    coins.value = response.data.data;
  } catch (error) {
    console.error("Error fetching data:", error);
  }
};

const paginatedCoins = computed(() => {
  const start = (currentPage.value - 1) * itemsPerPage;
  const end = start + itemsPerPage;
  return coins.value.slice(start, end);
});

const totalPages = computed(() => {
  return Math.ceil(coins.value.length / itemsPerPage);
});

const nextPage = () => {
  if (currentPage.value < totalPages.value) {
    currentPage.value++;
  }
};

const prevPage = () => {
  if (currentPage.value > 1) {
    currentPage.value--;
  }
};
</script>

<style scoped>
ion-grid {
  margin-top: 20px;
}
ion-row {
  border-bottom: 1px solid #ccc;
  padding: 10px 0;
}
ion-col {
  text-align: center;
}
</style>

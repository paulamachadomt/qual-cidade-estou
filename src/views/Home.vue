<template>
  <ion-page>
    <ion-header>
      <ion-toolbar>
        <ion-title>Em qual cidade estou?</ion-title>
      </ion-toolbar>
    </ion-header>

    <ion-content>
      <div class="container">
        <h2>Coordenadas</h2>
        <h3>{{latitude}}</h3>
        <h3>{{longitude}}</h3>
      </div>
    </ion-content>

  </ion-page>
</template>

<script lang="js">
import { IonContent, IonHeader, IonPage, IonTitle, IonToolbar } from '@ionic/vue';
import { defineComponent } from 'vue';
import { Geolocation } from '@capacitor/geolocation';

export default defineComponent({
  name: 'Home',
  components: {
    IonContent,
    IonHeader,
    IonPage,
    IonTitle,
    IonToolbar
  },
  data() {
    return {
      latitude: 0,
      longitude: 0
    }
  },
  ionViewWillEnter() {
    this.printCurrentPosition();
  },
  methods: {
    printCurrentPosition: async function() {
      const coordinates = await Geolocation.getCurrentPosition();
      console.log('Current position:', coordinates);

      this.latitude = coordinates.coords.latitude;
      this.longitude = coordinates.coords.longitude;
    }
  }
});
</script>

<style scoped>
.container {
  text-align: center;
}

</style>
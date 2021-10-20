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
        <ion-button @click="buscaLugar" expand="block">Qual cidade?</ion-button>
        <ul v-for="(cidade, index) in pegarCincoUltimosRegistros()" :key="index" >
          <li>{{cidade}}</li>          
        </ul>        
      </div>
    </ion-content>

  </ion-page>
</template>

<script lang="js">
import { IonContent, IonHeader, IonPage, IonTitle, IonToolbar, IonButton } from '@ionic/vue';
import { defineComponent } from 'vue';
import { Geolocation } from '@capacitor/geolocation';
import { Http } from '@capacitor-community/http';
import { Storage } from '@ionic/storage';

export default defineComponent({
  name: 'Home',
  components: {
    IonContent,
    IonHeader,
    IonPage,
    IonTitle,
    IonToolbar,
    IonButton
  },

  data() {
    return {
      latitude: 0,
      longitude: 0,
      cidade: '',
      localStorage: new Storage(),
      list: []
    }
  },

  created: async function() {
      await this.localStorage.create();
  },

  mounted: async function() {
    const list = await this.localStorage.get('list');
    
    if(list == null) {
      this.list = [];
    } 
    else {
      this.list = JSON.parse(list);
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
    },

    buscaLugar: async function() {
      const ACCESS_KEY = '5060464ccd39bf309c642f5d8e039907';
      
      const options = {
        url: `http://api.positionstack.com/v1/reverse?access_key=${ACCESS_KEY}&query=${this.latitude},${this.longitude}&limit=1`
      };

      const response = await Http.get(options);
console.log (response)
      
      this.cidade = response.data.data[0].locality + ', ' + response.data.data[0].region_code;
      
      if (this.cidade != '') {
        this.list.push(this.cidade);
      }
      
      this.localStorage.set('list', JSON.stringify(this.list))
        .then ( (value) => {
          console.log(value);
        })
        .catch( (error) => {
          console.log(error)
        })
     
    },

    pegarCincoUltimosRegistros: function() {
      return this.list.slice(Math.max(this.list.length -5, 0)).reverse();
    }
  }
});
</script>

<style scoped>
.container {
  text-align: center;  
}
ul {  
  padding:0;
}

</style>
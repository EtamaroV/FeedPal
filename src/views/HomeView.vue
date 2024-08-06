<script setup>
import mqtt from 'mqtt';

const protocol = 'ws'
const host = 'rasp.etamarov.me'
const port = '9001'
const path = '/mqtt'
const clientId = `mqtt_${Math.random().toString(16).slice(3)}`

const connectUrl = `${protocol}://${host}:${port}${path}`

const client = mqtt.connect(connectUrl, {
  clientId,
  clean: true,
  connectTimeout: 4000,
  username: 'feedpal',
  password: 'FeedPal1234567',
  reconnectPeriod: 1000,
})

client.on('connect', () => {
  console.log('Connected')
})

</script>

<template>
  <div class="main">Test ยังไม่เสร็จรอไปก่อน | In Development 🥺 

    <br>

    <v-btn @click="onRecheck">
    TEST
    </v-btn>

    <br>

    {{ connectett ? 'YES' : 'NO' }}
  </div>
  
  
</template>

<script scoped>


export default {
    data() {
        return {
          connectett: false,
        }
    },
    methods: {
        onRecheck() {
          if (navigator.onLine) {
            console.log("online");
            this.connectett = true
          } else {
            console.log("offline");
            this.connectett = false
          }
        },
    },
};

</script>

<style lang="scss">
  body {
    --corner-radius: 0px; /* Default value */
  }
  .main {
    padding-top: env(safe-area-inset-top);
    height: 720px;
  }
</style>

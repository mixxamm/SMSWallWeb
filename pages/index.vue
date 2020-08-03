<template>
  <v-layout
    column
  >
    <v-slide-y-transition group tag="v-row">
      <v-card
        v-for="(sms) in smsen"
        :key="sms.id"
        class="ma-2"
        :color="colors[sms.id % 7]"
        dark
        max-width="400"
      >
        <v-card-text class="headline font-weight-bold">
          {{ sms.sms }}
        </v-card-text>
      </v-card>
    </v-slide-y-transition>
  </v-layout>
</template>

<script>
const signalR = require('@microsoft/signalr')
const url = 'https://localhost:44372/smshub'

export default {
  data () {
    return {
      smsen: [],
      colors: ['#2196f3', '#f44336', '#00acc1', '#e91e63', '#9ccc65', '#ffa726', '#ff5722']
    }
  },
  created () {
    const connection = new signalR.HubConnectionBuilder().withUrl(url).build()
    connection.start().then(() => {
      console.log('Verbinding gestart')
    }).catch(err => console.error('Verbinding mislukt: ' + err))
    connection.on('ReceiveSMS', (sms) => {
      this.smsen.unshift({ sms, id: this.smsen.length })
    })
  }
}
</script>

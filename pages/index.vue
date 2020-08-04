<template>
  <div>
    <v-btn icon @click="showCode = !showCode">
      <v-icon>
        {{ showCode ? 'mdi-message-arrow-left' : 'mdi-qrcode' }}
      </v-icon>
    </v-btn>
    <v-container v-if="!showCode">
      <v-slide-y-transition group tag="v-row">
        <v-card
          v-for="(sms) in smsen"
          :key="sms.id"
          class="ma-2"
          :color="colors[sms.id % 7]"
          dark
          max-width="400"
        >
          <v-card-subtitle class="pb-0">
            {{ $moment(new Date(sms.date)).fromNow() }}
          </v-card-subtitle>
          <v-card-text class="headline font-weight-bold">
            {{ sms.message }}
          </v-card-text>
        </v-card>
      </v-slide-y-transition>
    </v-container>
    <v-container v-else fill-height>
      <v-row justify="center">
        <VueQrcode :value="sessie" :options="{width: 300}" />
      </v-row>
    </v-container>
  </div>
</template>

<script>
import VueQrcode from '@chenfengyuan/vue-qrcode'
const signalR = require('@microsoft/signalr')
const url = 'https://localhost:44372/smshub'

export default {
  components: {
    VueQrcode
  },
  data () {
    return {
      smsen: [],
      colors: ['#2196f3', '#f44336', '#00acc1', '#e91e63', '#9ccc65', '#ffa726', '#ff5722'],
      showCode: true,
      sessie: ''
    }
  },
  created () {
    this.sessie = Math.random().toString(36).substring(7)
    const connection = new signalR.HubConnectionBuilder().withUrl(url).build()
    connection.start().then(() => {
      console.log('Verbinding gestart')
      connection.invoke('AddToGroup', this.sessie)
    }).catch(err => console.error('Verbinding mislukt: ' + err))
    connection.on('ReceiveSMS', (sms) => {
      this.smsen.unshift({ ...sms, id: this.smsen.length })
    })
  }
}
</script>

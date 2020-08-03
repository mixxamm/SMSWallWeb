<template>
  <v-layout
    column
    justify-center
    align-center
  >
    <v-flex
      xs12
      sm8
      md6
    />
  </v-layout>
</template>

<script>
const signalR = require('@microsoft/signalr')
const url = 'https://localhost:44372/smshub'

const connection = new signalR.HubConnectionBuilder().withUrl(url).build()
connection.start().then(() => {
  console.log('Verbinding gestart')
}).catch(err => console.error('Verbinding mislukt: ' + err))
connection.on('ReceiveSMS', (sms) => {
  console.log(sms)
})
export default {
  data () {
    return {
      smsen: []
    }
  }
}
</script>

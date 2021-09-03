<template>
  <div>
    <v-btn icon @click="showCode = !showCode">
      <v-icon>
        {{ showCode ? 'mdi-message-arrow-left' : 'mdi-qrcode' }}
      </v-icon>
    </v-btn>
    <v-btn icon @click="singleLine = !singleLine">
      <v-icon>
        {{ singleLine ? 'mdi-view-day-outline' : 'mdi-view-column-outline' }}
      </v-icon>
    </v-btn>
    <div v-if="verbonden">
      <v-container v-if="!showCode">
        <div :class="singleLine ? 'single-line' : 'grid'">
          <v-card
            v-for="(sms) in smsen"
            :key="sms.id"
            class="ma-2 sms-card"
            :color="colors[sms.id % 7]"
            dark
            max-width="400"
            @click="smsen = smsen.filter(item => item.id != sms.id)"
          >
            <v-card-subtitle class="pb-0">
              {{ $moment(new Date(sms.date)).fromNow() }}
            </v-card-subtitle>
            <v-card-text class="headline font-weight-bold">
              {{ sms.message }}
            </v-card-text>
          </v-card>
        </div>
      </v-container>
      <v-container v-else fill-height>
        <v-row justify="center">
          <VueQrcode :value="code" :options="{width: 300}" />
        </v-row>
        <v-row justify="center" style="color: lightgreen">
          Sessie ID: {{JSON.parse(code).sessie}}
        </v-row>
        <v-row justify="center" class="text-center mt-4">
          <h4>Scan deze code met de SMSWall app</h4>
        </v-row>
        <v-row justify="center">
          <p>Download <a href="https://github.com/mixxamm/SMSWallNative/releases" target="_blank">hier</a> voor <v-icon>mdi-android</v-icon></p>
        </v-row>
      </v-container>
    </div>
    <div v-else class="text-center">
      <h1 v-if="err === ''">
        Verbinding maken met server
      </h1>
      <div v-else>
        <h1>
          Er is iets fout gegaan: {{ err }}
        </h1>
        <p>Herlaad de pagina om opnieuw te proberen.</p>
      </div>
    </div>
  </div>
</template>

<script>
import VueQrcode from '@chenfengyuan/vue-qrcode'
const signalR = require('@microsoft/signalr')
const url = 'https://api.mixxamm.ml/smshub'

export default {
  components: {
    VueQrcode
  },
  data () {
    return {
      smsen: [],
      colors: ['#2196f3', '#f44336', '#00acc1', '#e91e63', '#9ccc65', '#ffa726', '#ff5722'],
      showCode: true,
      singleLine: false,
      sessie: '',
      verbonden: false,
      err: ''
    }
  },
  computed: {
    code () {
      return JSON.stringify({ sessie: this.sessie, url })
    }
  },
  created () {
    this.sessie = Math.random().toString(36).substring(7)
    const connection = new signalR.HubConnectionBuilder().withUrl(url).build()
    connection.start().then(() => {
      console.log('Verbinding gestart')
      this.verbonden = true
      connection.invoke('AddToGroup', this.sessie)
    }).catch((err) => {
      console.error(err)
      this.err = err
    })
    connection.on('ReceiveSMS', (sms) => {
      this.smsen.unshift({ ...sms, id: this.smsen.length })
    })
  }
}
</script>

<style>
.grid{
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
  grid-column-gap: 24px;
  grid-row-gap: 12px;
}
.single-line{
  display: grid;
  place-items: center;
}
.sms-card{
  height: max-content;
  width: 100%;
  animation-name: appear;
  animation-duration: 1s;
}

@keyframes appear {
  from{
    transform: translateY(-100px);
  }
  to{
    transform: translateY(0px);
  }
}
</style>

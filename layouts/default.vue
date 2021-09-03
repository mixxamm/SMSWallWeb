<template>
  <v-app dark>
    <v-main>
      <nuxt />
    </v-main>
    <v-footer
      :absolute="!fixed"
      app
    >
      <a
        href="https://github.com/mixxamm"
        target="_blank"
        style="text-decoration: none;
    color: white!important;"
      ><v-icon class="mr-2">mdi-github</v-icon><span>mixxamm</span></a>
      <v-spacer></v-spacer><div @click="hideClock = !hideClock" :style="{opacity: hideClock ? '0%' : '100%', cursor: 'pointer'}">{{hours}}:{{minutes}}:{{seconds}}</div>
    </v-footer>
  </v-app>
</template>

<script>
export default {
  data () {
    return {
      hours: 0,
      minutes: 0,
      seconds: 0,
      hideClock: false
    }
  },
  methods: {
    setTime () {
      setInterval(() => {
        const date = new Date()
        this.hours = date.getHours()
        this.minutes = this.checkSingleDigit(date.getMinutes())
        this.seconds = this.checkSingleDigit(date.getSeconds())
      }, 1000)
    },
    checkSingleDigit (digit) {
      return ('0' + digit).slice(-2)
    }
  },
  created () {
    this.setTime()
  }
}
</script>

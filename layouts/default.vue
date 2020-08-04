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
    </v-footer>
  </v-app>
</template>

<script>
export default {
  data () {
    return {
    }
  }
}
</script>

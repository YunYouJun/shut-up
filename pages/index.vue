<template>
  <v-layout column justify-center align-center>
    <v-flex xs12 sm8 md6>
      <voice-chart></voice-chart>
    </v-flex>
  </v-layout>
</template>

<script>
import VoiceChart from '~/components/VoiceChart'
export default {
  components: {
    VoiceChart,
  },
  head() {
    return {
      title: '主页',
    }
  },
}
</script>

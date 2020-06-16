<template>
  <div class="text-center">
    <div id="voice-circle-container"></div>
    <v-col cols="12">
      <v-btn fab large :elevation="0" @click="toggleStatus">
        <v-icon>{{ statusIcon }}</v-icon>
      </v-btn>
    </v-col>
  </div>
</template>

<script>
import * as d3 from 'd3'
const linear = d3.scaleLinear().domain([0, 100]).range([0, 1])
const compute = d3.interpolateHcl('red', 'blue')
const innerRadius = 120
const angleStep = 0.04
export default {
  data() {
    return {
      isListening: false,
      fftSize: 1024,
      // small than half of fftSize
      // repeat display so size can be bigger
      displaySize: 1024,
      bars: [],
      statusIcon: 'mdi-play',
    }
  },
  mounted() {
    this.initVoiceData()
    this.initVoiceChart()
  },
  methods: {
    toggleStatus() {
      if (!this.isListening) {
        this.startListening()
        this.statusIcon = 'mdi-stop'
      } else {
        this.stopListening()
        this.statusIcon = 'mdi-play'
      }
      this.isListening = !this.isListening
    },
    initVoiceData() {
      this.data = new Array(this.displaySize)
      for (let i = 0; i < this.data.length; i++) {
        this.data[i] = 20
      }
    },
    initVoiceChart() {
      const width = 500
      const height = 500

      const svg = d3
        .select('#voice-circle-container')
        .append('svg')
        .attr('width', width)
        .attr('height', height)
        .attr('viewBox', `${-width / 2} ${-height / 2} ${width} ${height}`)
        .style('width', '100%')
        .style('height', 'auto')

      this.svg = svg

      svg
        .append('g')
        .selectAll('g')
        .data(this.data)
        .enter()
        .append('path')
        .attr('fill', (d) => compute(linear(d)))
        .attr('stroke', '#FFF')
        .attr('d', (d, i) => {
          return d3
            .arc()
            .innerRadius(innerRadius)
            .outerRadius(innerRadius + d / 3)
            .startAngle(i * angleStep)
            .endAngle((i + 1) * angleStep)()
        })
    },
    startListening() {
      this.initAudioContext()
      this.getVoice()
    },
    stopListening() {
      this.track.stop()
    },
    initAudioContext() {
      const AudioContext =
        window.AudioContext ||
        window.webkitAudioContext ||
        window.mozAudioContext ||
        window.msAudioContext
      this.audioContext = new AudioContext()
      // volume
      this.gainNode = this.audioContext.createGain()
      // analyser
      this.analyser = this.audioContext.createAnalyser()
      this.analyser.fftSize = this.fftSize
    },
    getVoice() {
      let source
      const audioCtx = this.audioContext
      const gainNode = audioCtx.createGain()
      const biquadFilter = audioCtx.createBiquadFilter()
      const analyser = this.analyser
      if (navigator.mediaDevices.getUserMedia) {
        const constraints = { audio: true }
        navigator.mediaDevices
          .getUserMedia(constraints)
          .then((stream) => {
            source = audioCtx.createMediaStreamSource(stream)
            source.connect(biquadFilter)
            biquadFilter.connect(gainNode)
            gainNode.connect(analyser)
            analyser.connect(audioCtx.destination)

            this.frequencyBars()
            // to stop
            this.track = stream.getTracks()[0]
          })
          .catch(function (err) {
            this.$toast.error('发生了什么奇怪的错误：' + err)
          })
      } else {
        this.$toast.error('你的浏览器不支持麦克风！')
      }
    },
    // Todo: input file
    frequencyBars() {
      const analyser = this.analyser
      const dataArray = new Uint8Array(this.displaySize)

      const drawBars = () => {
        analyser.getByteFrequencyData(dataArray)
        this.svg
          .selectAll('path')
          .data(dataArray)
          .attr('fill', (d) => compute(linear(d)))
          .attr('stroke', '#FFF')
          .attr('d', (d, i) => {
            return d3
              .arc()
              .innerRadius(innerRadius)
              .outerRadius(innerRadius + 3 + d / 2)
              .startAngle(i * angleStep - 1)
              .endAngle((i + 1) * angleStep - 1)()
          })

        window.requestAnimationFrame(drawBars)
      }
      window.requestAnimationFrame(drawBars)
    },
  },
}
</script>

<style></style>

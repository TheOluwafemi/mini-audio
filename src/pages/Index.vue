<template>
  <q-page class>
    <div class="audio-container">
      <div class="row q-ma-md">
        <div class="col-12">
          <div id="waveform"></div>
        </div>
      </div>
    </div>
    <div class="controls row flex flex-center fixed-bottom q-pb-md q-pt-md shadow-10">
      <div class>
        <q-btn
          color="primary"
          flat
          round
          icon="fast_rewind"
          size="xl"
          @click="wavesurfer.skipBackward(1)"
        />
        <q-btn
          v-if="isPlaying"
          color="primary"
          round
          icon="pause"
          size="xl"
          @click="wavesurfer.playPause()"
        />
        <q-circular-progress v-if="isLoading" size="72px" indeterminate color="primary" />
        <q-btn
        v-if="!isPlaying && !isLoading"
          color="primary"
          round
          icon="play_arrow"
          size="xl"
          @click="wavesurfer.playPause()"
        />
        <q-btn
          color="primary"
          flat
          round
          icon="fast_forward"
          size="xl"
          @click="wavesurfer.skipForward(1)"
        />
      </div>
    </div>
  </q-page>
</template>

<script>
import WaveSurfer from 'wavesurfer.js'
import { EventBus } from '../services/event-bus'

export default {
  name: 'PageIndex',
  data: () => ({
    wavesurfer: null,
    isLoading: false
  }),

  async mounted () {
    EventBus.$on('fileChosen', file => {
      this.loadFile(file)
    })

    if (!this.wavesurfer) {
      this.createWaveSurfer()
    }
  },
  methods: {
    createWaveSurfer () {
      this.wavesurfer = WaveSurfer.create({
        container: '#waveform',
        barWidth: 3,
        waveColor: 'white',
        hideScroller: true,
        progressColor: 'hsla(200, 100%, 30%, 0.5)',
        cusorColor: '#fff'
      })
      this.wavesurfer.load(
        'https://ia902606.us.archive.org/35/items/shortpoetry_047_librivox/song_cjrg_teasdale_64kb.mp3'
      )
      this.wavesurfer.on('ready', () => {
        this.isLoading = false
        this.wavesurfer.play()
      })
      this.wavesurfer.on('error', err => {
        console.error(err)
        this.isLoading = false
        this.$q.notify({ mesasge: err })
      })
      this.wavesurfer.on('loading', () => {
        this.isLoading = true
      })
    },

    loadFile (file) {
      if (file.target.files.length === 0) return
      this.wavesurfer.loadBlob(file.target.files[0])
    }
  },
  computed: {
    isPlaying () {
      if (!this.wavesurfer) return false
      return this.wavesurfer.isPlaying()
    }
  }
}
</script>

<style scoped>

.controls {
  background-color: white;
}

.audio-container {
  position: absolute;
  top: 0;
  left: 0;
  height: 100%;
  width: 100%;
  background-image: url('../assets/home.jpg');
  background-size: cover;
  background-repeat: no-repeat;
}

</style>

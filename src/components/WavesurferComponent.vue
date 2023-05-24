<template>
  <div>
    <div>
      <div ref="waveform1"></div>
    </div>
    <div>
      <div ref="waveform2"></div>
    </div>
    <div>
      <button @click="togglePlayback">{{ isPlaying ? 'Pause' : 'Play' }}</button>
      <button @click="stopPlayback">Stop</button>
    </div>
  </div>
</template>

<script>
import WaveSurfer from 'wavesurfer.js';
import mockData from '@/assets/mockdata.json';

export default {
  data() {
    return {
      isPlaying: false,
      seekPropagationEnabled: true,
    };
  },
  mounted() {
    // Initialize WaveSurfer instances
    this.waveform1 = WaveSurfer.create({
      container: this.$refs.waveform1,
      waveColor: 'violet',
      progressColor: 'purple',
    });

    this.waveform2 = WaveSurfer.create({
      container: this.$refs.waveform2,
      waveColor: 'violet',
      progressColor: 'purple',
    });

    // Load audio files and display waveforms
    this.loadAudio();

    // Synchronize seek action between waveforms
    this.waveform1.on('seek', () => this.syncWaveforms(this.waveform1));
    this.waveform2.on('seek', () => this.syncWaveforms(this.waveform2));
  },
  beforeUnmount() {
    // Clean up WaveSurfer instances
    this.waveform1.destroy();
    this.waveform2.destroy();
  },
  methods: {
    loadAudio() {
      const audio1 = require('@/assets/' + mockData.songs[0].parts[0].audio1);
      const audio2 = require('@/assets/' + mockData.songs[0].parts[0].audio2);

      // Load audio files into respective WaveSurfer instances
      this.waveform1.load(audio1);
      this.waveform2.load(audio2);
    },
    togglePlayback() {
      if (this.isPlaying) {
        // Pause both waveforms
        this.waveform1.pause();
        this.waveform2.pause();
      } else {
        // Get the current progress time of the first waveform
        const currentTime = this.waveform1.getCurrentTime();

        // Get the total duration of the audio files
        const duration = this.waveform1.getDuration();

        // Calculate the fraction of currentTime
        const fraction = currentTime / duration;

        // Set the same fraction for both waveforms
        this.seekPropagationEnabled = false;
        this.waveform1.seekTo(fraction);
        this.waveform2.seekTo(fraction);
        this.seekPropagationEnabled = true;

        // Start playing both waveforms
        this.waveform1.play();
        this.waveform2.play();
      }

      // Update the playback state
      this.isPlaying = !this.isPlaying;
    },
    stopPlayback() {
      // Pause both waveforms
      this.waveform1.pause();
      this.waveform2.pause();

      // Reset the seek bar position to the beginning
      this.seekPropagationEnabled = false;
      this.waveform1.seekTo(0);
      this.waveform2.seekTo(0);
      this.seekPropagationEnabled = true;

      // Update the playback state
      this.isPlaying = false;
    },
    syncWaveforms(wave) {
      if (this.seekPropagationEnabled) {
        // Get the current progress time of the waveform that triggered the "seek" event
        const currentTime = wave.getCurrentTime();

        // Get the total duration of the audio files
        const duration = wave.getDuration();

        // Calculate the fraction of currentTime
        const fraction = currentTime / duration;

        // Set the same fraction for the other waveform
        if (wave === this.waveform1) {
          this.seekPropagationEnabled = false;
          this.waveform2.seekTo(fraction);
          this.seekPropagationEnabled = true;
        } else if (wave === this.waveform2) {
          this.seekPropagationEnabled = false;
          this.waveform1.seekTo(fraction);
          this.seekPropagationEnabled = true;
        }
      }
    },
  },
};
</script>

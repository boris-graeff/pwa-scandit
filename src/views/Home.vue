<template>
  <div class="home">
    HOME
    <div class="scandit" ref="scandit"></div>

    {{ scan }}

    <audio ref="audio" src="/scan.mp3" />
  </div>
</template>

<script>
import { configure, BarcodePicker, ScanSettings } from 'scandit-sdk';
import config from '../../config.json';

export default {
  data() {
    return {
      scan: {},
    };
  },
  async mounted() {

    const engineLocation = '/build';
    await configure(config.scandit.apiKey, { engineLocation });

    this.barcodePicker = await BarcodePicker.create(this.$refs.scandit, {
      playSoundOnScan: false,
      vibrateOnScan: false,
      preloadEngineLibrary: true,
      preloadCameras: true,
    });

    const scanSettings = new ScanSettings({
      enabledSymbologies: ['qr', 'code128'],
      codeDuplicateFilter: 3000,
    });

    this.barcodePicker.applyScanSettings(scanSettings);

    this.barcodePicker.onScan((scan) => {
      this.$refs.audio.play();
      this.scan = scan.barcodes;
    });
  },
  beforeDestroy() {
    this.barcodePicker.destroy();
  },
  methods: {
    onScan(scan) {

    },
  },
};
</script>

<style lang="scss" scoped>
  .scandit {
    height: 50vh;
  }
</style>

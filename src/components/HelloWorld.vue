<template>
  <div class="wrap">
    <fieldset class="constraints">
      <legend>Video Constraints</legend>
      <label>width: <input type="number" v-model="width" /></label>
      <label>height: <input type="number" v-model="height" /></label>
      <label>framerate: <input type="number" v-model="frameRate" /></label>
    </fieldset>

    <div class="divider"></div>
    <button @click="requestCameraAuth">requestCameraAuth</button>

    <fieldset class="constraints flex">
      <legend>Select Device</legend>
      <label>
        video:
        <select v-if="deviceList.length > 0" v-model="selectedVideoDevice">
          <option
            v-for="(device, index) in deviceList"
            :key="index"
            :value="index"
          >
            {{ device.kind + ' ' + device.label }}
          </option>
        </select>
      </label>
      <label>
        audio:
        <select v-if="deviceList.length > 0" v-model="selectedAudioDevice">
          <option
            v-for="(device, index) in deviceList"
            :key="index"
            :value="index"
          >
            {{ device.kind + ' ' + device.label }}
          </option>
        </select>
      </label>
    </fieldset>

    <div class="divider"></div>
    <fieldset class="constraints">
      <legend>log:</legend>
      <pre>{{ this.deviceList[selectedVideoDevice] }}</pre>
      <br />
      <pre>{{ this.deviceList[selectedAudioDevice] }}</pre>
    </fieldset>

    <div class="divider"></div>
    <button @click="beginCapture">beginCapture</button>

    <div class="divider"></div>
    <video
      v-show="showVideo"
      autoplay
      controls
      ref="videoElem"
      class="cameravideo"
    ></video>
  </div>
</template>

<script>
import Plyr from 'plyr';
import 'plyr/dist/plyr.css';

export default {
  name: 'HelloWorld',
  props: {
    msg: String,
  },
  data() {
    return {
      deviceSelected: null,
      deviceList: [],

      width: 1920,
      height: 1080,
      frameRate: 60,
      showVideo: false,

      selectedVideoDevice: -1,
      selectedAudioDevice: -1,
    };
  },
  methods: {
    async requestDevice() {
      const device = await navigator.usb.requestDevice({
        filters: [
          {
            vendorId: 0x534d,
          },
        ],
      });
      console.log(device);
      await device.open();
      await device.selectConfiguration(1);
    },
    async requestCameraAuth() {
      navigator.mediaDevices.getUserMedia({
        video: true,
      });
      navigator.mediaDevices.getUserMedia({
        audio: true,
      });
      const devices = await navigator.mediaDevices.enumerateDevices();
      this.deviceList = devices;
      console.log(devices);
    },
    async beginCapture() {
      const media = await navigator.mediaDevices.getUserMedia({
        video: {
          deviceId: this.deviceList[this.selectedVideoDevice].deviceId,
          width: this.width,
          height: this.height,
          frameRate: this.frameRate,
        },
        audio: {
          deviceId: this.deviceList[this.selectedAudioDevice].deviceId,
        },
      });
      console.log(media);
      this.showVideo = true;

      this.$nextTick(() => {
        const player = new Plyr(this.$refs.videoElem, {
          controls: ['mute', 'volume', 'fullscreen'],
          keyboard: {
            focused: true,
            global: true,
          },
          autoplay: true,
          clickToPlay: false,
          listeners: {
            fullscreen: (e) => {
              if (!e.pointerType) {
                e.preventDefault();
              }
            },
          },
        });
        this.$refs.videoElem.srcObject = media;
        console.log(player);
      });
    },
  },
};
</script>

<style scoped>
.wrap {
  width: 800px;
  margin: auto;
}
.flex {
  display: flex;
}
.constraints {
  text-align: left;
}
.constraints label {
  margin-right: 10px;
}
.divider {
  height: 20px;
}
.cameravideo {
  max-width: 100%;
}
</style>

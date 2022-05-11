<template>
  <div class="wrap">
    <fieldset class="constraints">
      <legend>Video Constraints</legend>
      <label>width: <input type="number" v-model="width" /></label>
      <label>height: <input type="number" v-model="height" /></label>
      <label>framerate: <input type="number" v-model="frameRate" /></label>
    </fieldset>

    <div class="divider"></div>
    <button @click="requestCameraAuth" style="margin-right: 10px">
      requestCameraAuth
    </button>
    <select v-if="deviceList.length > 0" @change="selectDevice">
      <option v-for="(device, index) in deviceList" :key="index" :value="index">
        {{ device.kind + ' ' + device.label }}
      </option>
    </select>
    <div class="divider"></div>
    <video v-show="showVideo" controls ref="videoElem" autoplay muted></video>
  </div>
</template>

<script>
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
    };
  },
  methods: {
    async selectDevice(event) {
      const device = this.deviceList[event.target.value];
      const media = await navigator.mediaDevices.getUserMedia({
        video: {
          deviceId: device.deviceId,
          width: this.width,
          height: this.height,
          frameRate: this.frameRate,
        },
      });
      this.$refs.videoElem.srcObject = media;
      this.showVideo = true;
      // this.deviceSelected = devices[0];
      // for (let device of devices) addDevice(device);
      // selectDevice(devices[0]);
    },
    async requestCameraAuth() {
      const media = navigator.mediaDevices.getUserMedia({
        video: true,
      });
      const devices = await navigator.mediaDevices.enumerateDevices();
      this.deviceList = devices;
      console.log(devices);
      return media;
    },
    async beginCapture() {
      const media = navigator.mediaDevices.getUserMedia({
        video: true,
      });
      return media;
    },
  },
};
</script>

<style scoped>
.wrap {
  width: 800px;
  margin: auto;
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
</style>

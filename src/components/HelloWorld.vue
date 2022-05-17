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
    <div ref="videoWrapper" class="videowrapper" allow>
      <video
        v-show="showVideo"
        autoplay
        ref="videoElem"
        class="cameravideo"
      ></video>
    </div>
    <div class="divider"></div>
    <button v-show="showVideo" @click="requestfull">fullscreen video</button>
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
        video: {
          //  vendorId: 0x534d,
        },
      });
      navigator.mediaDevices.getUserMedia({
        audio: {
          //  vendorId: 0x534d,
        },
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
      this.$refs.videoElem.srcObject = media;
      this.showVideo = true;
    },
    requestfull() {
      if (this.$refs.videoWrapper.webkitRequestFullscreen) {
        this.$refs.videoWrapper.webkitRequestFullscreen();
      } else if(this.$refs.videoWrapper.requestFullscreen()) {
        this.$refs.videoWrapper.requestFullscreen();
      }
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
.videowrapper:fullscreen {
 width: 100%;
 height: 100%;
 position: fixed;
 top:0;
 left: 0;
 bottom: 0;
 right: 0;
 display: flex;
 align-items: center;
 justify-content: center;
 background-color: rgba(255, 255, 255, 0.5);
}
.videowrapper:fullscreen .cameravideo {
  max-height: 100%;
}
</style>

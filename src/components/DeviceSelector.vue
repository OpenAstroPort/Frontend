<template>
  <div class="container">
    <div class="row" v-for="device in devices" :key="device">
      <div class="form-check form-check-reverse">
        <input class="form-check-input" name="devices" :value="device" type="radio" :id="device"
               v-model="selectedDevice">
        <label class="form-check-label" :for="device">{{ device }}</label>
      </div>
    </div>
    <div class="row">
      <label>Baudrate:</label>
      <select v-model="selectedBaudRate">
        <option v-for="baudRate in validBaudRates" :key="baudRate" :value="baudRate">{{ baudRate }}</option>
      </select>
    </div>
    <div class="row">
      <button class="btn btn-primary" @click="selectDevice">Select</button>
    </div>
  </div>
</template>

<script>
const axios = require("axios")

export default {
  name: "DeviceSelector",
  emits: [
      "device-select-success",
      "device-select-error"
  ],
  created() {
    const self = this;
    axios.get(process.env.VUE_APP_ROOT_API + "/devices")
        .then((response) => {
          self.devices = response.data.result;
        })
        .catch((error) => {
          self.error = "issue fetching available com devices, please check logs for more information";
          console.error(error);
        });
  },
  data() {
    return {
      validBaudRates: [
        300,
        1200,
        2400,
        4800,
        9600,
        19200,
        38400,
        57600,
        74880,
        115200,
        230400,
        250000,
        500000,
        100000
      ],
      selectedDevice: null,
      selectedBaudRate: 19200,
      devices: []
    }
  },
  methods: {
    selectDevice() {
      const self = this;
      axios.post(process.env.VUE_APP_ROOT_API + "/devices", {
        "comDevice": self.selectedDevice,
        "baudRate": self.selectedBaudRate
      }).then((response) => {
        if (response.data.status === "success") {
          this.$emit('device-select-success')
        } else if (response.data.status === "error") {
          this.$emit('device-select-error')
        }
      }).catch((error) => {
        console.error(error);
      })
    }
  }
}
</script>

<style scoped>

label {
  text-align: left !important;
}

</style>
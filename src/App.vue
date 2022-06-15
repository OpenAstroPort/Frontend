<template>
  <div class="container">
    <div class="row">
      <div class="col">
        <h2> OAT Mobile Control</h2> 
      </div>
    </div>
    <div class="alert alert-dark top-buffer" v-show="!deviceConnected">
      <h5>No Device is connected yet please connect one first:</h5>
      <device-selector v-on:device-select-success="initApplication"></device-selector>
    </div>
    <div v-show="testResult" class="alert-success">
      <pre>
        {{testResult}}
      </pre>
    </div>
    <div class="row top-buffer">
      <div class="col">Curent position</div>
    </div>
    <div class="row">
      <div class="col">RA: {{currentRA}}</div>
    </div>
    <div class="row">
      <div class="col">DEC: {{currentDecAngle}}° {{currentDecTimeInMin}}</div>
    </div>
    <div class="row">
      <div class="col">
           <button class="btn btn-primary" @click="getCurrentRa();">Get RA</button>
      </div>
    </div>
    <div class="row top-buffer">
      <div class="col">
        <button type="button" class="btn btn-primary btn-xl">
          <i class="bi bi-caret-up"></i>
        </button>
      </div>
    </div>
    <div class="row top-buffer gx-5">
      <div class="col right-buffer d-flex justify-content-end">
        <button type="button" class="btn btn-primary btn-xl">
          <i class="bi bi-caret-left"></i>
        </button>
      </div>
      <div class="col left-buffer d-flex justify-content-start">
        <button type="button" class="btn btn-primary btn-xl">
          <i class="bi bi-caret-right"></i>
        </button>
      </div>
    </div>
    <div class="row top-buffer">
      <div class="col">
        <button type="button" class="btn btn-primary btn-xl">
          <i class="bi bi-caret-down"></i>
        </button>
      </div>
    </div>
    <div class="row top-buffer">
      <div class="col d-grid">
        <button class="btn btn-primary" @click="setHome();">Set Home</button>
      </div>
      <div class="col d-grid">
        <button class="btn btn-primary" @click="setTarget();">Set Target</button>
      </div>
      <div class="col d-grid">
        <button class="btn btn-primary">Stop</button>
      </div>
    </div>
    <div class="row top-buffer">
      <div class="col allign-left">
        <label for="slewRate" class="form-label">Slew Rate: </label>
        <span id="showSlewRate">{{ slewRate }}</span>
        <input type="range" class="form-range" min="1" max="5" id="slewRate" @change="updateSlewRate($event)">
      </div>
    </div>
    <div class="row top-buffer">
      <div class="col allign-left">
        <div class="form-check form-switch form-check-reverse">
          <input class="form-check-input" type="checkbox" role="switch" id="trackingSwitch" @click="activateTracking();">
          <label class="form-check-label" for="trackingSwitch">Tracking</label>
        </div>
        <div class="form-check form-switch form-check-reverse top-buffer">
          <input class="form-check-input" type="checkbox" role="switch" id="parkSwitch">
          <label class="form-check-label" for="parkSwitch">Park</label>
        </div>
        <div class="form-check form-check-reverse top-buffer">
          <input class="form-check-input" type="checkbox" value="" id="reverseCheck1">
          <label class="form-check-label" for="reverseCheck1">
            Reverse checkbox
          </label>
        </div>
      </div>
    </div>
  </div>
</template>

<script>

const axios = require("axios");

import DeviceSelector from "@/components/DeviceSelector";

export default {
  name: 'App',
  data() {
    return {
      slewRate: 5,
      currentDecAngle: 0,
      currentDecTimeInMin: 0,
      currentRA: Date,
      deviceConnected: false,
      isHomeSet: false,
      isTargetSet: false,
      testResult: null
    }
  },

  components: {
    DeviceSelector
  },

  methods: {
    updateSlewRate: function (val) {
      this.slewRate = val.target.value;
    },

    getCurrentDecAngle: function () {
      this.currentDecAngle = 10;
      //TODO: API ansprechen
    },

    getCurrentDecTimeInMin: function () {
      this.currentDecTimeInMin = 40;
      //TODO: API ansprechen
    },

    getCurrentRa: function () {
      this.currentRA = Date.now();
      //TODO: API ansprechen
    },

    activateTracking: function() {
      var checkbox = document.getElementById("trackingSwitch");
      if (checkbox.checked == true) {
        alert("test");
      }
    },

    setHome: function() {
      this.isHomeSet = true;
      alert("Home is Set");
    },

    setTarget: function() {
      this.isTargetSet = true;
      alert("Target is Set");
    },

    initApplication: function () {
      this.getCurrentRa();
      this.deviceConnected = true;
      // TODO: Show Success alert and display some telescope infos
      const self = this;
      axios.get(process.env.VUE_APP_ROOT_API + "/telescope/info").then((response) => {
        if(response.data.status === "success")
          self.testResult = JSON.stringify(response.data.result);
      })
    }
  }
}
</script>

<style>
body {
  font-family: Helvetica;
  -webkit-font-smoothing: antialiased;
  text-align: center;
  background-color: #212a2e;
  color: #F7F8F8;
  margin-top: 10px;
}

.checkbox-xl {
  transform: scale(1, 4);
}

.allign-left {
  text-align: left;
}

.btn-xl {
  padding: 0.3rem 1rem;
  font-size: 50px;
  border-radius: 18px;
}

.top-buffer {
  margin-top: 1.5rem;
}

.left-buffer {
  margin-left: 1.5rem;
}

.right-buffer {
  margin-right: 1.5rem;
}
</style>
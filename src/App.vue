<template>
  <div class="container">
    <div class="row">
      <div class="col">
        <h2> OAT Mobile Control</h2> 
      </div>
    </div>
    <div class="alert alert-dark top-buffer" v-show="!deviceSelected">
      <h5>No Device is connected yet please connect one first:</h5>
      <device-selector v-on:device-select-success="initApplication"></device-selector>
    </div>
    <div v-show="testResult" class="alert-success">
      <pre>
        {{testResult}}
      </pre>
    </div>
    <div class="row top-buffer">
      <div class="col">
        <button class="btn btn-primary" v-show="deviceConnected" @click="disconnectDevice">disconnect</button>
        <button class="btn btn-primary" v-show="!deviceConnected" @click="connectDevice">connect</button>
      </div>
    </div>
    <div class="row top-buffer">
      <div class="col">Curent position</div>
    </div>
    <div class="row top-buffer-small">
      <div class="col">RA: {{currentRightAscension}}</div>
    </div>
    <div class="row">
      <div class="col">DEC: {{currentDeclination}}</div>
    </div>
    <div class="row top-buffer">
      <div class="col">
        <button type="button" class="btn btn-primary btn-xl" @touchstart="startMoveDirection('n')" @touchend="stopMoveDirection('n')" @mousedown="startMoveDirection('n')" @mouseup="stopMoveDirection('n')">
          <i class="bi bi-caret-up"></i>
        </button>
      </div>
    </div>
    <div class="row top-buffer gx-5">
      <div class="col right-buffer d-flex justify-content-end">
        <button type="button" class="btn btn-primary btn-xl" @touchstart="startMoveDirection('w')" @touchend="stopMoveDirection('w')" @mousedown="startMoveDirection('w')" @mouseup="stopMoveDirection('w')">
          <i class="bi bi-caret-left"></i>
        </button>
      </div>
      <div class="col left-buffer d-flex justify-content-start">
        <button type="button" class="btn btn-primary btn-xl" @touchstart="startMoveDirection('e')" @touchend="stopMoveDirection('e')" @mousedown="startMoveDirection('e')" @mouseup="stopMoveDirection('e')">
          <i class="bi bi-caret-right"></i>
        </button>
      </div>
    </div>
    <div class="row top-buffer">
      <div class="col">
        <button type="button" class="btn btn-primary btn-xl" @touchstart="startMoveDirection('s')" @touchend="stopMoveDirection('s')" @mousedown="startMoveDirection('s')" @mouseup="stopMoveDirection('s')">
          <i class="bi bi-caret-down"></i>
        </button>
      </div>
    </div>
    <div class="row top-buffer">
      <div class="col d-grid">
        <button class="btn btn-primary" @click="setHome();">Set Home</button>
      </div>
      <div class="col d-grid">
        <button class="btn btn-primary" data-bs-toggle="modal" data-bs-target="#setTarget">Slew to Target</button>
        <div class="modal fade" id="setTarget" tabindex="-1" aria-labelledby="setTargetLabel" aria-hidden="true">
          <div class="modal-dialog modal-dialog-centered">
            <div class="modal-content">
              <div class="modal-header">
                <h5 class="modal-title" id="setTargetLabel">Set Target</h5>
                <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
              </div>
              <div class="modal-body">
                <div class="row">
                  <label for="inputRA1">RA: </label>
                  <div class="col"><input id="inputRA1" class="form-control" type="text" placeholder="00h" aria-label="default input example"></div>
                  <div class="col"><input id="inputRA2" class="form-control" type="text" placeholder="00m" aria-label="default input example"></div>
                  <div class="col"><input id="inputRA3" class="form-control" type="text" placeholder="00s" aria-label="default input example"></div>
                </div>
                <div class="row top-buffer">
                  <label for="inputDEC1">DEC: </label>
                  <div class="col"><input id="inputDEC1" class="form-control" type="text" placeholder="+/-00°" aria-label="default input example"></div>
                  <div class="col"><input id="inputDEC2" class="form-control" type="text" placeholder="00m" aria-label="default input example"></div>
                  <div class="col"><input id="inputDEC3" class="form-control" type="text" placeholder="00s" aria-label="default input example"></div>
                </div>
              </div>
              <div class="modal-footer">
                <button type="button" class="btn btn-secondary" data-bs-dismiss="modal">Close</button>
                <button type="button" class="btn btn-primary" @click="setTarget();">LET IT SLEW!</button>
              </div>
            </div>
          </div>
        </div>
      </div>
      <div class="col d-grid">
        <button class="btn btn-primary" @click="stopMoveAllDirection();">Stop</button>
      </div>
    </div>
    <div class="row top-buffer">
      <div class="col d-grid">
        <button class="btn btn-primary" @click="slewTo('home');">Home</button>
      </div>
      <div class="col d-grid">
        <button class="btn btn-primary" id="toggleParking" @click="toggleParking('toggleParking');">Park</button>
      </div>
    </div>
    <div class="row top-buffer">
      <div class="col allign-left">
        <label for="slewRate" class="form-label">Slew Rate: </label>
        <span id="showSlewRate">{{ slewRate }}</span>
        <input type="range" class="form-range" min="1" max="4" id="slewRate" @change="updateSlewRate($event)">
      </div>
    </div>
    <div class="row top-buffer">
      <div class="col allign-left">
        <div class="form-check form-switch form-check-reverse">
          <input class="form-check-input" type="checkbox" role="switch" id="toggleTracking" @click="activateTracking('toggleTracking');" checked>
          <label class="form-check-label" for="toggleTracking">Tracking</label>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import DeviceSelector from "@/components/DeviceSelector";
import $ from "../node_modules/jquery";

const axios = require("axios");

$(".modal").on("hidden.bs.modal", function(){
    $(".modal-body1").html("");
});


export default {
  name: 'App',
  data() {
    return {
      slewRate: 4,
      currentDeclination: "",
      currentRightAscension: "",
      deviceConnected: false,
      deviceSelected: false,
      isHomeSet: false,
      isTargetSet: false,
      testResult: null,
      isSlewing: false,
      isTracking: false
    }
  },

  components: {
    DeviceSelector
  },

  methods: {

    autoRunnerLocation: function () {
      //this.autorunner = setInterval(this.getCurrentDecAndRA, 2000);
      this.getCurrentDecAndRA();
    },

    slewTo: function (target) {
      axios.post(process.env.VUE_APP_ROOT_API + "/telescope/slew", {to: target}).then((response) => {
        if(response.data.status === "success") {
          console.log(response);
        }
      })
    },

    updateSlewRate: function (slewRate) {
      this.slewRate = slewRate.target.value;
      axios.post(process.env.VUE_APP_ROOT_API + "/telescope/slew/speed", {speed: this.slewRate}).then((response) => {
        if(response.data.status === "success") {
          console.log(response);
        }
      })
    },

    getCurrentDecAndRA: function () {
      axios.get(process.env.VUE_APP_ROOT_API + "/telescope/position").then((response) => {
        if(response.data.status === "success") {
          this.currentRightAscension = response.data.result.rightAscension;
          this.currentDeclination = response.data.result.declination;
        }
      })
    },

    stopMoveAllDirection: function() {
      axios.post(process.env.VUE_APP_ROOT_API + "/telescope/move/quit", {direction: "a"}).then((response) => {
        if(response.data.status === "success") {
          console.log(response);
        }
      })
    },

    startMoveDirection: function (direction) {
      // const self = this;
      event.preventDefault();
      axios.post(process.env.VUE_APP_ROOT_API + "/telescope/move", {direction: direction}).then((response) => {
        // if(response.data.status === "success")
        //   self.isMoving = true;
        console.log(response);
      })
    },

    stopMoveDirection: function (direction) {
      // const self = this;
      axios.post(process.env.VUE_APP_ROOT_API + "/telescope/move/quit", {direction: direction}).then((response) => {
        // if(response.data.status === "success")
        //   self.isMoving = false;
        console.log(response);
      })
    },

    activateTracking: function(tracking) {
      axios.post(process.env.VUE_APP_ROOT_API + "/telescope/action", {action: tracking}).then((response) => {
        if(response.data.status === "success"){
          console.log(response);
        }
      })   
    },

    toggleParking: function(parking) {
      axios.post(process.env.VUE_APP_ROOT_API + "/telescope/action", {action: parking}).then((response) => {
        if(response.data.status === "success"){
          console.log(response);
        }
      })
      document.getElementById("toggleTracking").checked = false;
    },

    setHome: function() {
      axios.post(process.env.VUE_APP_ROOT_API + "/telescope/action", {action: "setHome"}).then((response) => {
          if(response.data.status === "success"){
            console.log(response);
          }
        })
    },

    setTarget: function() {
      this.isTargetSet = true;
      const targetRA = document.getElementById("inputRA1").value + ':' + document.getElementById("inputRA2").value + ':' + document.getElementById("inputRA3").value;
      const targetDEC = document.getElementById("inputDEC1").value + '*' + document.getElementById("inputDEC2").value + '\'' + document.getElementById("inputDEC3").value;
      axios.post(process.env.VUE_APP_ROOT_API + "/target/position", {declination: targetDEC, rightAscension: targetRA}).then((response) => {
        if(response.data.status === "success"){
          console.log(response.result);
        }
      })
      this.slewTo("target");
    },

    connectDevice: function () {
      const self = this;
      this.autoRunnerLocation(this.autorunner);      
      return new Promise(function(resolve, reject) {
        axios.post(process.env.VUE_APP_ROOT_API + "/devices/action", {"action": "connect"}).then((response) => {
          if(response.data.status === "success") {
            self.deviceConnected = true;
            resolve(response.data);
          } else {
            reject(response.data);
          }
        }).catch((error) => {
          reject(error);
        }).then (() => {
          axios.get(process.env.VUE_APP_ROOT_API + "/telescope/status").then((response) => {
            this.isSlewing = response.data.result.isSlewing;
            this.isTracking = response.data.result.isTracking;
            document.getElementById("toggleTracking").checked = this.isTracking;
            this.getCurrentDecAndRA();
          });
        })
      });
    },

    disconnectDevice: function () {
      const self = this;
      clearInterval(this.autorunner);
      return new Promise(function(resolve, reject) {
        axios.post(process.env.VUE_APP_ROOT_API + "/devices/action", {"action": "disconnect"}).then((response) => {
          if(response.data.status === "success") {
            self.deviceConnected = false;
            resolve(response.data);
          } else {
            reject(response.data);
          }
        }).catch((error) => {
          reject(error);
        })
      });
    },

    initApplication: function () {
      this.deviceSelected = true;
      this.connectDevice();
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
  margin-top: 1rem;
  margin-bottom:  2rem;
}

.modal-content {
  background-color: #212a2e;
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

.top-buffer-small {
  margin-top: 0.5rem;
}

.left-buffer {
  margin-left: 1.5rem;
}

.right-buffer {
  margin-right: 1.5rem;
}
</style>
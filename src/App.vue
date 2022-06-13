<template>
<div class="container">
  <div class="row">
    <div class="col"> OAT Mobile Controll</div>
  </div>
  <div class="row top-buffer">
    <div class="col">Curent position</div>
  </div>
  <div class="row">
    <div class="col">RA: 00h 00m 00s</div>
  </div>
  <div class="row">
    <div class="col">DEC: 00° 00m 00s</div>
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
      <button class="btn btn-primary">set home</button>
    </div>
    <div class="col d-grid">
      <button class="btn btn-primary">set target</button>
    </div>
    <div class="col d-grid">
      <button class="btn btn-primary">stop</button>
    </div>
  </div>
  <div class="row top-buffer">
    <div class="col allign-left">
      <label for="slewRate" class="form-label">Slew Rate: </label>
      <span id="showSlewRate">{{slewRate}}</span>
      <input type="range" class="form-range" min="1" max="5" id="slewRate" @change="updateSlewRate($event)">
    </div>
  </div>
  <div class="row top-buffer">
    <div class="col allign-left">
      <div class="form-check form-switch form-check-reverse">
        <input class="form-check-input" type="checkbox" role="switch" id="trackingSwitch">
        <label class="form-check-label" for="trackingSwitch">tracking</label>        
      </div>
      <div class="form-check form-switch form-check-reverse top-buffer">
        <input class="form-check-input" type="checkbox" role="switch" id="parkSwitch">
        <label class="form-check-label" for="parkSwitch">park</label>
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

export default {
  name: 'App',
  data() {
    return{
      slewRate: 5
    }
  },

  components: {
  },

  methods: {
    updateSlewRate: function(val) {
      this.slewRate = val.target.value; 
    }
  }
}
</script>

<style>
body {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  background-color: #212a2e;
  color: #F7F8F8;
  margin-top: 10px;
}

.checkbox-xl {
  transform: scale(1,4);
}
.allign-left{
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

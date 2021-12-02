<template>
  <div class="container">
    <div class="card">
      <div class="card-header text-center p-4">
        <h3 class="text-info">FIND FLIGHT WITH US</h3>
      </div>
      <div class="card-body text-center">
        <h5 class="card-title">Welcome to TravelBD</h5>
        <div class="card-text row text-center">
          <div class="col d-box text-center">
            <br />
            <span class="text-muted">
              Leaving From <br />
              <i class="fas fa-plane-departure"></i>
            </span>
            <br />
            <br />
            <div class="input-group mb-3">
              <input
                type="text"
                class="form-control text-center"
                placeholder="FROM"
                aria-label="Departure"
              />
            </div>
          </div>
          <div class="col d-box text-center">
            <br />
            <span class="text-muted">
              Going To <br />
              <i class="fas fa-plane-arrival"></i>
            </span>
            <br />
            <br />
            <div class="input-group mb-3">
              <input
                type="text"
                class="form-control text-center"
                placeholder="TO"
                aria-label="Arrival"
              />
            </div>
          </div>
          <div class="col d-box text-center">
            <br />
            <span class="text-muted"> Departing On </span>
            <div class="input-group mb-3">
              <input
                type="text"
                class="form-control text-center"
                placeholder="Pick a Date"
                aria-label="Departure On"
              />
            </div>
            <hr class="my-4" />
            <span class="text-muted"> Returning On </span>
            <div class="input-group mb-3">
              <input
                type="text"
                class="form-control text-center"
                placeholder="Pick a Date"
                aria-label="Returning On"
              />
            </div>
          </div>
        </div>
        <br />
        <a href="#" class="btn btn-info" v-on:click="changeValue()">Find me a flight</a>
      </div>
    </div>
  </div>
</template>

<style scoped>
.container {
  margin: 300px;
  font-family: sans-serif;
}

.card {
  margin: auto;
}
.d-box {
  background-color: whitesmoke;
  border-right: 6px solid grey;
}
</style>
<script>
export default {
  methods:{
    changeValue(){
      this.bool=false;
    }
  }
};
</script>

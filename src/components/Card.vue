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
            <div class="input-group mb-3" style="position:relative;">
              <input
                v-model="from"
                type="text"
                class="form-control text-center"
                placeholder="FROM"
                aria-label="Departure"
                @input="srchfrom()"
              />
            </div>
            <div class="from-list" v-if="status_flight_from" style="position:absolute;" id="flightfrom-code-list">
              <tr v-for="(flight, index) in flight_code" :key="index" style="cursor:pointer; background-color:whitesmoke;">
                <td scope="col" @click="setFlightFrom(flight[2])">
                  <b> {{ flight[1] }}</b> ,<b>{{ flight[2] }}</b> ,
                  {{ flight[0] }}
                  <hr>
                </td>
              </tr>
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
            <div class="input-group mb-3" style="position:relative;">
              <input
                v-model="to"
                type="text"
                class="form-control text-center"
                placeholder="TO"
                aria-label="Arrival"
                @input="srchto()"
              />
            </div>
            <div class="from-list" v-if="status_flight_to" style="position:absolute;" id="flightto-code-list">
              <tr v-for="(flight, index) in flight_code" :key="index" style="cursor:pointer; background-color:whitesmoke;">
                <td scope="col" @click="setFlightTo(flight[2])">
                  <b> {{ flight[1] }}</b> ,<b>{{ flight[2] }}</b> ,
                  {{ flight[0] }}
                  <hr>
                </td>
              </tr>
            </div>
          </div>
          <div class="col d-box text-center">
            <br />
            <span class="text-muted"> Departing On </span>
            <Datepicker
              v-model="depdate"
              class="text-center"
              :disabled-dates="states.disabledDates"
              :placeholder="pick"
            ></Datepicker>
            <hr class="my-4" />
            <span class="text-muted"> Returning On </span>
            <Datepicker
              v-model="arrdate"
              class="text-center mb-4"
              :disabled-dates="states.disabledDates"
              :placeholder="pick"
            ></Datepicker>
          </div>
        </div>
        <br />
        <a href="#" class="btn btn-info" v-on:click="getFlight()"
          >Find me a flight</a
        >
      </div>
    </div>
    <div class="flightlist" v-if="display == true">
      <table class="table table-striped table-hover table-info">
        <thead>
          <tr>
            <th scope="col">Flight</th>
            <th scope="col">Departure Time</th>
            <th scope="col">Arrival Time</th>
            <th scope="col">Duration</th>
            <th scope="col">Price</th>
          </tr>
        </thead>
        <tbody>
          <tr>
            <th scope="row">1</th>
            <td>Mark</td>
            <td>Otto</td>
            <td>@mdo</td>
          </tr>
          <br />
        </tbody>
      </table>
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
hr{
  background-color:grey;
}
.flightlist {
  margin: auto;
  display: flex;
}
</style>
<script>
import Datepicker from "vuejs-datepicker";
import moment from "moment";
import { autocomplete } from "air-port-codes-node";

export default {
  data() {
    return {
      //form data
      status_flight_from: null,
      status_flight_to: null,
      flight_code: [],
      depdate: null,
      arrdate: null,
      from: null,
      to: null,
      //form data
      //row display
      display: null,
      //row display
      pick: "Pick a Date",
      states: {
        disabledDates: {
          to: new Date(Date.now() - 86400000),
        },
      },
      apca: autocomplete({
        key: "2f0586afe2",
        secret: "552cd5f87dca62a", // Your API Secret Key: use this if you are not connecting from a web server
        limit: 15,
      }),
    };
  },
  methods: {
    getFlight() {
      this.display = true;
      var url =
        "https://api.sharetrip.net/api/v1/flight/search?tripType=Return&adult=1&child=0&infant=0&class=Economy&origin=" +
        this.from +
        "&destination=" +
        this.to +
        "&depart=" +
        moment(this.depdate).format("YYYY-MM-DD") +
        "&depart=" +
        moment(this.arrdate).format("YYYY-MM-DD");
      //alert(this.from+"\n"+this.to+"\n"+moment(this.depdate).format("YYYY-MM-DD")+"\n"+moment(this.arrdate).format("YYYY-MM-DD"))
      //console.log(this.from, this.to, moment(this.depdate).format("YYYY-MM-DD"), moment(this.arrdate).format("YYYY-MM-DD"));
      //console.log(url);
    },
    srchfrom() {
      this.status_flight_from = true;
      this.apca.request(this.from);
      // SUCCESS we found some airports
      this.apca.onSuccess = (data) => {
        if (data.airports.length > 0  && this.from.length>0) {
          document.getElementById('flightfrom-code-list').style.display="block";
          this.flight_code = [];
          for (let i = 0; i < data.airports.length; i++) {
            //console.log("data", data.airports[i].name);
            //console.log("data", data.airports[i].city);
            //console.log("data", data.airports[i].iata);
            this.flight_code[i] = [
              data.airports[i].name,
              data.airports[i].city,
              data.airports[i].iata,
            ];
          }
        }
      };
      // FAIL no airports found
      this.apca.onError = (data) => {
        console.log("onError", data.message);
      };
    },
    srchto() {
      this.status_flight_to = true;
      this.apca.request(this.to);
      // SUCCESS we found some airports
      this.apca.onSuccess = (data) => {
        if (data.airports.length > 0 && this.to.length>0) {
          document.getElementById('flightto-code-list').style.display="block";
          this.flight_code = [];
          for (let i = 0; i < data.airports.length; i++) {
            //console.log("data", data.airports[i].name);
            //console.log("data", data.airports[i].city);
            //console.log("data", data.airports[i].iata);
            this.flight_code[i] = [
              data.airports[i].name,
              data.airports[i].city,
              data.airports[i].iata,
            ];
          }
        }
      };
      // FAIL no airports found
      this.apca.onError = (data) => {
        console.log("onError", data.message);
      };
    },
    setFlightFrom(flight){
      this.from=flight;
      document.getElementById('flightfrom-code-list').style.display="none";
    },
    setFlightTo(flight){
      this.to=flight;
      document.getElementById('flightto-code-list').style.display="none";
    }
  },
  components: {
    Datepicker,
  },
};

//date
</script>

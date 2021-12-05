<template>
  <div class="container">
    <div class="card">
      <div class="card-header text-center p-4">
        <h3 class="text-info">FIND FLIGHT WITH US</h3>
      </div>
      <div class="card-body text-center">
        <h5 class="card-title">Welcome to TravelBD</h5>
        <div class="card-text row text-center">
          <div class="col d-box text-center" style="position: relative">
            <br />
            <span class="text-muted">
              Leaving From <br />
              <i class="fas fa-plane-departure"></i>
            </span>
            <br />
            <br />
            <div class="input-group mb-3">
              <input
                v-model="from"
                type="text"
                class="form-control text-center"
                placeholder="FROM"
                aria-label="Departure"
                @input="srchfrom()"
              />
            </div>
            <p id="fromcity"></p>
            <div
              class="from-list"
              v-if="status_flight_from"
              style="position: absolute; z-index: 1000"
              id="flightfrom-code-list"
            >
              <table>
                <tr
                  v-for="(flight, index) in flight_code_from"
                  :key="index"
                  style="cursor: pointer; background-color: whitesmoke"
                >
                  <td scope="col" @click="setFlightFrom(flight)">
                    <b> {{ flight[1] }}</b> ,<b>{{ flight[2] }}</b> ,
                    {{ flight[0] }}
                  </td>
                </tr>
              </table>
            </div>
          </div>
          <div class="col d-box text-center" style="position: relative">
            <br />
            <span class="text-muted">
              Going To <br />
              <i class="fas fa-plane-arrival"></i>
            </span>
            <br />
            <br />
            <div class="input-group mb-3">
              <input
                v-model="to"
                type="text"
                class="form-control text-center"
                placeholder="TO"
                aria-label="Arrival"
                @input="srchto()"
              />
            </div>
            <p id="tocity"></p>
            <div
              class="from-list"
              v-if="status_flight_to"
              style="position: absolute; z-index: 1000"
              id="flightto-code-list"
            >
              <tr
                v-for="(flight, index) in flight_code_to"
                :key="index"
                style="cursor: pointer; background-color: whitesmoke"
              >
                <td scope="col" @click="setFlightTo(flight)">
                  <b> {{ flight[1] }}</b> ,<b>{{ flight[2] }}</b> ,
                  {{ flight[0] }}
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
    <div class="card card-info" id="weather_display" style="display: none">
      <div class="card-header">
        <p id="weather_city"></p>
      </div>
      <div class="card-body">
        <p class="card-text" id="weather"></p>
      </div>
    </div>
    <div id="loading" style="margin-top: 50px; display: none">
      <PulseLoader style="margin: auto"></PulseLoader>
    </div>
    <div class="flightlist" v-if="display == true">
      <table style="width: 100%" class="table table-info">
        <tr>
          <th></th>
          <th>Flight</th>
          <th>
            Departure<br /><button @click="sortDeparture()">
              Sort by Departure
            </button>
          </th>
          <th>
            Duration<br /><button @click="sortDuration()">
              Sort by Duration
            </button>
          </th>
          <th>
            Arrival<br /><button @click="sortArrival()">Sort by Arrival</button>
          </th>
          <th>
            Price<br /><button @click="sortPrice()">Sort by Price</button>
          </th>
        </tr>
        <tr
          class="bg-info"
          v-for="(flight, index) in flight_list"
          :key="index"
          style="cursor: pointer"
        >
          <th>
            GOING
            <hr />
            RETURING
          </th>
          <td>
            {{ flight[0][0] }}<br />{{ flight[0][1] }}
            <hr />
            {{ flight[1][0] }}<br />{{ flight[1][1] }}
          </td>
          <td>
            {{ flight[0][2] }}<br />{{ flight[0][3] }}<br />{{ flight[0][4] }}
            <hr />
            {{ flight[1][6] }}<br />{{ flight[1][7] }}<br />{{ flight[1][8] }}
          </td>
          <td>
            <i class="fas fa-plane-arrival"></i>
            {{ flight[0][5] }}
            <hr />
            <i class="fas fa-plane-arrival"></i>
            {{ flight[1][5] }}
          </td>
          <td>
            {{ flight[0][6] }}<br />{{ flight[0][7] }}<br />{{ flight[0][8] }}
            <hr />
            {{ flight[1][2] }}<br />{{ flight[1][3] }}<br />{{ flight[1][4] }}
          </td>
          <td>{{ flight[0][9] }} BDT</td>
        </tr>
      </table>
    </div>
  </div>
</template>

<style scoped>
.container {
  text-align: center;
  font-family: sans-serif;
}

.card {
  margin: auto;
}
.d-box {
  background-color: whitesmoke;
  border-right: 6px solid grey;
}
.flightlist {
  margin: auto;
  display: flex;
}
table,
th,
tr,
td {
  border: 2px solid grey;
  text-align: center;
}
.d-row {
  border: solid 1px #404040;
  padding: 10px;
}
</style>
<script>
import Datepicker from "vuejs-datepicker";
import moment from "moment";
import { autocomplete } from "air-port-codes-node";
import PulseLoader from "vue-spinner/src/PulseLoader.vue";

export default {
  data() {
    return {
      //form data
      status_flight_from: null,
      status_flight_to: null,
      flight_code_from: [],
      flight_code_to: [],
      depdate: null,
      arrdate: null,
      from: null,
      to: null,
      destination: null,
      //form data
      //flight display
      display: null,
      flight_list: [],
      //flight display
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
      document.getElementById("loading").style.display = "flex";
      document.getElementById("weather_display").style.display = "none";
      this.display = false;
      var url =
        "https://api.sharetrip.net/api/v1/flight/search?tripType=Return&adult=1&child=0&infant=0&class=Economy&origin=" +
        this.from +
        "&destination=" +
        this.to +
        "&depart=" +
        moment(this.depdate).format("YYYY-MM-DD") +
        "&depart=" +
        moment(this.arrdate).format("YYYY-MM-DD");
      fetch(url)
        .then((response) => response.json())
        .then((data) => {
          var flight_dep_arr = [];
          var flight_dep_row;
          var flight_arr_row;
          var airName;
          var airCode;
          var depCode;
          var depTime;
          var depDate;
          var duration;
          var arrCode;
          var arrTime;
          var arrDate;
          var price;
          var i = 0;
          data.response.flights.forEach((flights) => {
            airName = flights.flight[0].airlines.short;
            depCode = this.from;
            depTime = flights.flight[0].departureDateTime.time;
            depDate = flights.flight[0].departureDateTime.date;
            duration = flights.flight[0].duration;
            arrCode = this.to;
            arrTime = flights.flight[0].arrivalDateTime.time;
            arrDate = flights.flight[0].arrivalDateTime.date;
            price = flights.price;
            airCode =
              flights.segments[0].segment[0].airlinesCode +
              " " +
              flights.segments[0].segment[0].flightNumber;
            flight_dep_row = [
              airName,
              airCode,
              depCode,
              depTime,
              depDate,
              duration,
              arrCode,
              arrTime,
              arrDate,
              price,
            ];
            airName = flights.flight[1].airlines.short;
            depCode = this.from;
            depTime = flights.flight[1].departureDateTime.time;
            depDate = flights.flight[1].departureDateTime.date;
            duration = flights.flight[1].duration;
            arrCode = this.to;
            arrTime = flights.flight[1].arrivalDateTime.time;
            arrDate = flights.flight[1].arrivalDateTime.date;
            price = flights.price;
            airCode =
              flights.segments[1].segment[0].airlinesCode +
              " " +
              flights.segments[1].segment[0].flightNumber;
            flight_arr_row = [
              airName,
              airCode,
              depCode,
              depTime,
              depDate,
              duration,
              arrCode,
              arrTime,
              arrDate,
              price,
            ];
            //console.log(flight_dep_row + "***********" + flight_arr_row);
            flight_dep_arr[i++] = [flight_dep_row, flight_arr_row];
            //console.log(flight_row[0] + "\n" + flight_row[1]);
          });
          //console.log("************************************************");
          //console.log(flight_dep_arr);

          this.flight_list = flight_dep_arr;
          //console.log(flight_dep_arr);
          flight_dep_arr = [];
          if (this.flight_list.length == data.response.flights.length) {
            document.getElementById("loading").style.display = "none";
            this.display = true;
            //console.log(this.flight_list);
          }
        })
        .catch((error) => {
          alert("Opps! There is no flight available. Try again!");
          document.getElementById("weather_display").style.display = "none";
          document.getElementById("loading").style.display = "none";
        });
      //for(let i=0;i<this.flight_list.length;i++){
      //  console.log("i"+ this.flight_list[i]);
      //}
      if (this.destination.length > 0) {
        fetch(
          "https://weatherapi-com.p.rapidapi.com/current.json?q=" +
            this.destination,
          {
            method: "GET",
            headers: {
              "x-rapidapi-host": "weatherapi-com.p.rapidapi.com",
              "x-rapidapi-key":
                "ceff00788dmshc572d2e939e2aa1p16f562jsn6a57c668e74a",
            },
          }
        )
          .then((response) => response.json())
          .then((data) => {
            document.getElementById("weather_display").style.display = "flex";
            document.getElementById(
              "weather_city"
            ).innerHTML = `Weather for <b>${this.destination}</b>`;
            document.getElementById(
              "weather"
            ).innerHTML = `${data.current.temp_c} ℃ <br> ${data.current.temp_f} ℉ 
                                                          <br> ${data.current.condition.text} <br> <img src="${data.current.condition.icon}"/>`;
            //console.log(countr.toLowerCase() +" " +cit.toLowerCase() +" " +this.destination +" " +data.current.temp_f
            //+" " +data.current.temp_c +" " +data.current.condition.text +" " +data.current.condition.icon);
          })
          .catch((err) => {
            console.error(err);
          });
      }
    },
    srchfrom() {
      this.status_flight_from = true;
      this.apca.request(this.from);
      // SUCCESS we found some airports
      this.apca.onSuccess = (data) => {
        if (data.airports.length > 0 && this.from.length > 0) {
          document.getElementById("flightfrom-code-list").style.display =
            "block";
          this.flight_code_from = [];
          for (let i = 0; i < data.airports.length; i++) {
            this.flight_code_from[i] = [
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
        if (data.airports.length > 0 && this.to.length > 0) {
          document.getElementById("flightto-code-list").style.display = "block";
          this.flight_code_to = [];
          for (let i = 0; i < data.airports.length; i++) {
            this.flight_code_to[i] = [
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
    setFlightFrom(flight) {
      this.from = flight[2];
      document.getElementById("flightfrom-code-list").style.display = "none";
      document.getElementById(
        "fromcity"
      ).innerHTML = `${flight[0]}<br><b>${flight[1]}</b>`;
    },
    setFlightTo(flight) {
      this.to = flight[2];
      this.destination = flight[1];
      document.getElementById("flightto-code-list").style.display = "none";
      document.getElementById(
        "tocity"
      ).innerHTML = `${flight[0]}<br><b>${flight[1]}</b>`;
    },
    sortPrice() {
      //flight_list.sort(function(a,b)=>)
      //console.log(this.flight_list);
      this.flight_list.sort(function (a, b) {
        return a[0][9] - b[0][9];
      });
    },
    sortDuration() {
      //flight_list.sort(function(a,b)=>)
      //console.log(this.flight_list);
      this.flight_list.sort(function (a, b) {
        if (a[0][5] < b[0][5]) return -1;
        if (a[0][5] > b[0][5]) return 1;
        return 0;
      });
    },
    sortDeparture() {
      //flight_list.sort(function(a,b)=>)
      //console.log(this.flight_list);
      this.flight_list.sort(function (a, b) {
        if (a[0][3] < b[0][3]) return -1;
        if (a[0][3] > b[0][3]) return 1;
        return 0;
      });
    },
    sortArrival() {
      //flight_list.sort(function(a,b)=>)
      //console.log(this.flight_list);
      this.flight_list.sort(function (a, b) {
        if (a[0][7] < b[0][7]) return -1;
        if (a[0][7] > b[0][7]) return 1;
        return 0;
      });
    },
  },
  components: {
    Datepicker,
    PulseLoader,
  },
};

//date
</script>

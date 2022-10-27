<script>
import Clock from "../components/Clock.vue";

export default {
  name: "HomeView",
  components: {
    Clock,

  },
  data() {
    return {
      stations: null
    }
  },
  mounted() {
    // fetch data from the API every 0 seconds
    this.getStations();
  },
  methods: {
    getStations() {
      var apiUrl = "https://api.tfl.gov.uk/StopPoint/Mode/tube";
        const res = fetch(apiUrl).then((response) => {
          return response.json();
        }).then((data) => {
          // if the stoppointNaptan doesn't start with 940GZZLU, remove it
          // this is a hack to remove the non-tube stations
          data = data.stopPoints.filter((station) => {
            console.log(station)
            return station.naptanId.startsWith("940GZZLU");
          });
          // Add a property that is the last three digits of the naptanId
          // this is used to create the URL
          data.forEach((station) => {
            station.shortId = station.naptanId.slice(-3);
            station.goodName = station.commonName.replace(" Underground Station", "");
          });
          
          this.stations = data;
        });
    },
  }
};
</script>

<template>
  <main>
    
      <div class="flex w-full px-2">
        <ul class="flex w-full flex-row flex-wrap">
          <li class="w-full sm:w-1/2 md:w-1/3 2xl:w-1/4" v-for="station, indx in stations" v-bind:key="indx">
            <a :href="'/'+station.shortId">
              <div class="py-1">
                <div class="text-lg text-white font-bold">{{station.goodName}}</div>
              </div>
            </a>
          </li>
        </ul>
      </div>
   <Clock />
  </main>
</template>

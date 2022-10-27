<template>
  <div class="w-full flex flex-row flex-wrap-reverse md:flex-wrap">
    <div class="w-full md:w-1/4 bg-gray-800 text-white p-4">
      <a href="/">
        Home
      </a>
      <a class="py-1" :href="'/'+$route.params.station">
       &rarr; {{ this.stationName }}
      </a>
      <a v-if="$route.params.line" class="py-1 uppercase" :href="'/'+$route.params.station+'/'+$route.params.line">
       &rarr; {{ $route.params.line }}
      </a>
      <a v-if="$route.params.direction" class="py-1" :href="'/'+$route.params.station+'/'+$route.params.line+'/'+$route.params.direction">
       &rarr; {{ $route.params.direction }}
      </a>
      <div v-if="lines" class="text-white font-semibold border-b border-gray-600 my-2 pb-2 mt-4">
        Lines
      </div>
      <div v-for="line, lindx in lines" v-bind:key="lindx" class="text-white inline pr-2 pb-2 mt-4 text-sm font-semibold uppercase">
        <a :class="'bg-'+line.lineName" class="px-2 py-1" :href="'/'+$route.params.station+'/'+line.lineName">
          {{ line.lineName }}
        </a>
      </div>
      <div v-if="$route.params.line" class="text-white font-semibold border-b border-gray-600 pb-2 mt-4">
        Options
      </div>
      <div v-if="$route.params.line" class="text-white pb-2 mt-4 flex flex-row">
        <a class="w-1/2 block text-center" :href="'/'+$route.params.station+'/'+$route.params.line+'/inbound'">
          Inbound
        </a>
        <a class="w-1/2 block text-center" :href="'/'+$route.params.station+'/'+$route.params.line+'/outbound'">
          Outbound
        </a>
      </div>
      
      

    </div>
    <ul v-if="trainData" class="w-full md:w-3/4 rounded-md px-4 md:px-8 max-w-xl mx-auto dotmatrix"> 
      <li v-for="train, indx in trainData" v-bind:key="indx">
        <SingleTrain :train="train" />
      </li>
    </ul>
    <div v-else>
      <div class="text-white text-center">Loading data...</div>
    </div>
    </div>
</template>

<script>
import SingleTrain from "../components/SingleTrain.vue";

export default {
  components: {
    SingleTrain,
  },
  props: {
    msg: {
        type: String,
        required: true,
      },
  },
  data() {
    return {
      trainData: null,
      lines: null,
      stationName: null,
    }
  },
  mounted() {
    // fetch data from the API every 0 seconds
    this.getTrainData();
    this.getStationLines();
  },
  methods: {
    getStationLines() {
      // str to uppr
      var station = this.$route.params.station.toUpperCase();
      var apiUrl = 'https://api.tfl.gov.uk/StopPoint/ServiceTypes?id=940GZZLU'+station+'&modes=tube'
      // Fetch train data from the API
        const res = fetch(apiUrl).then((response) => {
          return response.json();
        }).then((data) => {
          // Sort data by due time
          
          this.lines = data;
          
        });
        
    },
     getTrainData() {
      var station = this.$route.params.station.toUpperCase();
      var apiUrl = 'https://api.tfl.gov.uk/StopPoint/940GZZLU'+station+'/arrivals'
      if(this.$route.params.line) {
        apiUrl = 'https://api.tfl.gov.uk/Line/'+this.$route.params.line+'/Arrivals/940GZZLU'+station
      }
      if(this.$route.params.direction) {
        apiUrl = 'https://api.tfl.gov.uk/Line/'+this.$route.params.line+'/Arrivals/940GZZLU'+station+'?direction='+this.$route.params.direction
      }
      if(this.$route.params.destination) {
        apiUrl = 'https://api.tfl.gov.uk/Line/'+this.$route.params.line+'/Arrivals/940GZZLU'+station+'?direction='+this.$route.params.direction+'&destinationStationId=940GZZLU'+this.$route.params.destination
      }
      // Fetch train data from the API
        
        console.log("updating");
        const res = fetch(apiUrl).then((response) => {
          return response.json();
        }).then((data) => {
          // Sort data by due time
          data.sort((a, b) => (a.timeToStation > b.timeToStation) ? 1 : -1)
          this.trainData = data;
          // Remove Underground Station from name if it exists
          this.stationName = data[0].stationName.replace(" Underground Station", "");
          setTimeout(this.getTrainData, 15000);
        });
        
    },
  }
};
</script>

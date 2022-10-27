<template>
  <div class="w-full flex flex-row flex-wrap-reverse md:flex-wrap">
    <div class="w-full md:w-1/4 bg-gray-800 text-white p-4">
      <a href="/"> Home </a>
      
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
    };
  },
  mounted() {
    // fetch data from the API every 0 seconds
    // this.getTrainData();
    // this.getStationLines();
  },
  methods: {
   
  },
};
</script>

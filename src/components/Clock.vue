<template>
   <div class="dotmatrix text-amber-500 text-center py-4">{{ time }}</div>
</template>

<script>

export default {
  data() {
    return {
      time: "00:00:00"
    }
  },
  mounted() {
    // fetch data from the API every 0 seconds
    this.getTime();
  },
  methods: {
    getTime() {
        var x = new Date();
        this.time = x.toLocaleTimeString();
        setTimeout(this.getTime, 1000);
    },
  }
};
</script>

<template>
  <div class="visualizer-container">
    <div
      v-for="(value, index) in frequencyBands"
      :key="index"
      class="visualizer-bar"
      :style="{
        height: (value) * 100 + '%', // Scale value to percentage
        backgroundColor: barColor,
        marginRight: index < frequencyBands.length - 1 ? barSpacing + 'px' : '0px',
      }"
    ></div>
  </div>
</template>

<script>
export default {
  props: {
    frequencyBands: {
      type: Array,
      required: true,
    },
    barColor: {
      type: String,
      default: '#00ff00',
    },
    barSpacing: {
      type: Number,
      default: 2, // Spacing between bars in pixels
    },
  },
};
</script>

<style scoped>
.visualizer-container {
  display: flex;
  align-items: flex-end;
  justify-content: center;
  width: 100%;
  height: 100%; /* Adjust based on your design */
  overflow: hidden; /* Ensure no overflow if bars are too large */
}

.visualizer-bar {
  width: 10px; /* Adjust based on the number of frequency bands */
  transition: height 0.05s ease; /* Smooth transition for bar height */
}
</style>
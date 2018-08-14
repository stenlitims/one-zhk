<template>
  <div>
    <vue-slider v-model="value" v-bind="options"></vue-slider>
  </div>
</template>
<script>
import vueSlider from "vue-slider-component";

export default {
  components: {
    vueSlider
  },
  data() {
    return {
      value: [1, 9],
      options: {
        width: "100%",
        height: 8,
        dotSize: 20,
        min: 1,
        max: 9,
        disabled: false,
        show: true,
        tooltip: "always"
      }
    };
  }
};
</script>
<template>
  <LottieAnimationPlayer
    @lottie-loaded="lottieLoaded"
    :animationFile="animationFile"
    :loopAnimation="false"
    :autoplay="false"
    ><slot
  /></LottieAnimationPlayer>
</template>
<script>
export default {
  data() {
    return {
      container: {},
    };
  },
  props: {
    animationFile: { type: String, required: true },
    containerProp: {},
    scrollLimitsMode: { type: String, default: "window" },
    minScrollPercentage: { type: Number, default: 0 },
    maxScrollPercentage: { type: Number, default: 1 },
  },
  methods: {
    lottieLoaded(lottieInstance) {
      this.container = this.containerProp;
      if (!this.container) {
        this.container = this.$parent;
      }
      if (this.container.$el) {
        this.container = this.container.$el;
      }
      window.addEventListener("scroll", () => {
        this.redrawScroll(lottieInstance);
      });
      this.redrawScroll(lottieInstance);
    },
    redrawScroll(lottieInstance) {
      const rawPercent = this.getScrollPercentage();
      let percent;
      if (rawPercent <= this.minScrollPercentage) {
        percent = 0;
      } else if (rawPercent >= this.maxScrollPercentage) {
        percent = 1;
      } else {
        percent =
          (rawPercent - this.minScrollPercentage) / this.maxScrollPercentage;
      }
      const totalFrames = lottieInstance.totalFrames - 1;
      const frame = Math.ceil(totalFrames * percent);
      lottieInstance.goToAndStop(frame, true);
    },
    getScrollPercentage() {
      if (this.scrollLimitsMode === "window") {
        const h = document.documentElement,
          b = document.body,
          st = "scrollTop",
          sh = "scrollHeight";
        let p = (h[st] || b[st]) / ((h[sh] || b[sh]) - h.clientHeight);
        if (p < 0) {
          p = 0;
        }
        if (p > 1) {
          p = 1;
        }
        return p;
      } else {
        throw new Error("unsupported scroll mode");
      }
    },
  },
};
</script>
<style scoped>

</style>
<template>
  <div class="lottie-animation-player" ref="player"><slot /></div>
</template>
<script>
import lottie from "lottie-web/build/player/lottie";
export default {
  props: {
    animationFile: { type: String, required: true },
    loopAnimation: { type: Boolean, default: false },
    autoplay: { type: Boolean, default: true }
  },
  mounted() {
    let lottieInstance = lottie.loadAnimation({
      container: this.$refs.player,
      renderer: "svg",
      loop: this.loopAnimation,
      autoplay: this.autoplay,
      path: this.animationFile
    });
    // OR data-ready (?)
    lottieInstance.addEventListener("config_ready", () => {
      this.$emit("lottie-loaded", lottieInstance);
    });
  }
};
</script>
<style scoped>
/* .lottie-animation-player {
    min-height: 100vh;
 
} */
</style>

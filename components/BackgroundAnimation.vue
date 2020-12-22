<template>
  <div>
    <slot name="header" />
    <slot />
    <MultipartAnimationPlayer
      id="map"
      animationFile="marketing.json"
      :segments="segments"
      :timeSpeedRatio="1"
      :maxTime="7"
      :maxSpeed="5"
      ref="map"
    />

    <slot name="footer" />
  </div>
</template>

<script>
export default {
  data() {
    return {
      relativePositionCallbacks: [],
      pageSegmentMap: {
        "": 0,
        development: 2,
        marketing: 4,
      },
      segments: [
        {
          startFrame: 0,
          endFrame: 30,
          autoAdvanceSegmentIndex: 1,
          autoplay: false,
          registerController: (callback) => {},
        },
        {
          startFrame: 30 + 1,
          endFrame: 8 * 30,
          autoplay: false,

          registerController: (callback) => {
            this.relativePositionCallbacks.push(callback);
          },
        },
        {
          startFrame: 8 * 30 + 1,
          endFrame: 10 * 30,
          autoplay: true,
          autoAdvanceSegmentIndex: 3,
          registerController: (callback) => {},
        },
        {
          startFrame: 10 * 30 + 1,
          endFrame: 14 * 30,
          autoplay: false,
          registerController: (callback) => {
            this.relativePositionCallbacks.push(callback);
          },
        },
        {
          startFrame: 14 * 30,
          endFrame: 20 * 30 - 1,
          autoplay: false,
          registerController: (callback) => {
            this.relativePositionCallbacks.push(callback);
          },
        },
      ],
    };
  },
  methods: {
    changeSegment(n) {
      this.$refs.map.goToSegment(n);
    },
    relativePositionChanged(newVal) {
      for (const relativePositionCallback of this.relativePositionCallbacks) {
        relativePositionCallback(newVal);
      }
    },
    redrawScroll() {
      const rawPercent = this.getScrollPercentage();
      let percent = rawPercent;

      this.relativePositionChanged(percent);
    },
    getScrollPercentage() {
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
    },
  },
  mounted() {
    window.addEventListener("scroll", () => {
      this.redrawScroll();
    });
  },
  watch: {
    $route(to, from) {
      const toParts = to.path.split("/");
      const fromParts = from.path.split("/");
      if (toParts[1] === fromParts[1]) {
        return;
      }
      if (this.pageSegmentMap[toParts[1]] !== undefined) {
        const targetSegmentID = this.pageSegmentMap[toParts[1]];
        this.$refs.map.goToSegment(targetSegmentID);
      }
    },
  },
};
</script>

<style>
#map {
  /*
  position: fixed;
  top: 50%;
  left: 50%;
  width: 100%;
  height: 100%;
  transform: translate(-50%, -50%);
  z-index: -10;
  background-color: #111827;
  */
  position: sticky;
  bottom: 0%;
  z-index: 3;
  /*background-color: #111827;*/
}
</style>

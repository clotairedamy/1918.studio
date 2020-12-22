<template>
  <LottieAnimationPlayer
    @lottie-loaded="lottieLoaded"
    :animationFile="animationFile"
    :loopAnimation="false"
    :autoplay="false"
  >
  </LottieAnimationPlayer>
</template>

<script>
export default {
  /*
    segment structure:
    {
        startFrame: number,
        endFrame: number,
        autoAdvanceSegmentIndex: number|undefined,
        autoplay: boolean,

        // function is called by the player (this component)
        // argument is supplied by the player (this component) and should be called to set the relative position (0-100%) of the current segment
        registerController((positionInSegment: number) => {...}) 
    }
    */
  data() {
    return {
      lottieInstance: null,
      currentSegment: 0,
      currentFrame: 0,
      cachedOperations: [],
      /*
      sub-segment structure:
      {
        startFrame: number, // startframe > endFrame signals reverse play
        endFrame: number,
        speed: number // multiplier
      }
       */
      pendingSubSegments: []
    };
  },
  props: {
    animationFile: { type: String, required: true },
    fps: { type: Number, default: 30 },
    segments: { required: true },
    timeSpeedRatio: { type: Number, default: 0.5 }, // 0=constant time, 1=constant speed
    maxTime: { type: Number, default: 5 }, //seconds
    maxSpeed: { type: Number, default: 5 }, //default speed multiplier
  },
  methods: {
    lottieLoaded(lottieInstance) {
      this.goToSegment(0);
      this.lottieInstance = lottieInstance;
      lottieInstance.addEventListener("enterFrame", event => {
        this._onFrameUpdate(event.currentTime);
      });
      let segmentIndex = 0;
      for (const segment of this.segments) {
        const segICopy = segmentIndex;
        if (segment.registerController) {
          segment.registerController((positionInSegment) => {
            if (this.currentSegment !== segICopy) {
              return;
            } else {
              this._setPositionInSegment(positionInSegment);
            }
          });
        }
        segmentIndex += 1;
      }
      for (const cachedOperation of this.cachedOperations) {
        cachedOperation();
      }
    },
    goToSegment(segmentNumber) {
      if (this.lottieInstance === null) {
        this.cachedOperations.push(() => {
          this.goToSegment(segmentNumber);
        });
        return;
      }
      if (segmentNumber < 0 || segmentNumber >= this.segments.length) {
        return;
      }
      const segment = this.segments[segmentNumber];
      this.pendingSubSegments = this._generateSubsegments(segment);
      this.currentSegment = segmentNumber;
      if (this.pendingSubSegments.length > 0) {
        this._playSubsegment(this.pendingSubSegments[0]);
      }

    },
    _generateSubsegments(segment) {
      const subSegments = [];
      const firtsStartFrame = this.currentFrame;
      let firstEndFrame = segment.startFrame;
      if (segment.endFrame < this.currentFrame) {
        firstEndFrame = segment.endFrame;
      }
      const normalSpeedTime = Math.abs(this.currentFrame - firstEndFrame) / this.fps;
      let constantTimeSpeedMultiplier = 1;
      if (normalSpeedTime > this.maxTime) {
        constantTimeSpeedMultiplier = normalSpeedTime / this.maxTime;
      }
      const speedMultiplier = (1 - this.timeSpeedRatio) * constantTimeSpeedMultiplier + this.timeSpeedRatio * this.maxSpeed;
      subSegments.push({startFrame: firtsStartFrame, endFrame: firstEndFrame, speed: speedMultiplier});
      if (segment.autoplay && firstEndFrame !== segment.endFrame) {
        subSegments.push({startFrame: segment.startFrame, endFrame: segment.endFrame, speed: 1});
      }
      return subSegments;
    },
    _getSubsegmentDirection(subSegment) {
      let direction = 1;
      if (subSegment.startFrame >= subSegment.endFrame) {
        direction = -1;
      }
      return direction;
    },
    _playSubsegment(subSegment) {
      let direction = this._getSubsegmentDirection(subSegment);
      this.lottieInstance.setDirection(direction);
      this.lottieInstance.setSpeed(subSegment.speed);
      this.lottieInstance.goToAndPlay(subSegment.startFrame, true);
    },
    _setPositionInSegment(positionInSegment) {
      if (this.lottieInstance === null) {
        this.cachedOperations.push(() => {
          this._setPositionInSegment(positionInSegment);
        });
        return;
      }
      const segment = this.segments[this.currentSegment];
      const firstFrame = segment.startFrame;
      const offset = (segment.endFrame - firstFrame) * positionInSegment;
      this.lottieInstance.goToAndStop(Math.round(firstFrame + offset), true);
    },
    _onFrameUpdate(newFrame) {
      this.currentFrame = newFrame;
      if (this.pendingSubSegments.length > 0) {
        const subSegment = this.pendingSubSegments[0];
        let direction = this._getSubsegmentDirection(subSegment);
        if ((direction === 1 && this.currentFrame >= subSegment.endFrame) ||
        (direction === -1 && this.currentFrame <= subSegment.endFrame)) {
          this._onSubsegmentFinished();
        }
      }
    },
    _onSubsegmentFinished() {
      this.pendingSubSegments.shift();
      if (this.pendingSubSegments.length > 0) {
        this._playSubsegment(this.pendingSubSegments[0]);
      } else {
        this._onSegmentFinished();
      }
    },
    _onSegmentFinished() {
      const segment = this.segments[this.currentSegment];
      if (segment.autoAdvanceSegmentIndex !== undefined) {
        this.goToSegment(segment.autoAdvanceSegmentIndex);
      } else {
        this.lottieInstance.pause();
      }
    }
  },
};
</script>
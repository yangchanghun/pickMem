<template>
  <div class="step-two">
    <div class="mb-5">
      <div
        v-else-if="!isLoading && canPhoto"
        class="d-flex justify-content-center align-items-center"
      >
        <div class="camera-horizontal camera-frame">
          <div class="col-custom">
            <div class="camera-wrapper">
              <video ref="camera" width="600" height="450" autoplay></video>

              <canvas
                ref="canvas"
                width="600"
                height="450"
                style="display: none"
              ></canvas>

              <!-- 중앙 카운트다운 -->
              <div v-if="countdown > 0" class="countdown-overlay">
                {{ countdown }}
              </div>

              <!-- 찰칵 -->
              <div v-if="showSnap" class="snap-text">찰칵!</div>

              <!-- 플래시 -->
              <div v-if="flash" class="flash"></div>
            </div>
          </div>

          <div class="col-custom" style="width: 100px">
            <div
              class="w-100 d-flex flex-column align-items-center justify-content-center"
              style="height: 450px"
            >
              <div
                v-if="!isAutoShooting && getImageLen < 6"
                class="takePic d-flex justify-content-center align-items-center"
                @click="startCountdown"
              >
                <div class="takePic-inner"></div>
              </div>

              <div v-if="isAutoShooting" style="color: white; margin-top: 20px">
                자동 촬영 중...
              </div>

              <div
                v-if="getImageLen == 6"
                style="color: yellow; margin-top: 20px"
              >
                촬영 완료!
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      images: {},

      countdown: 0,
      countdownTimer: null,

      autoInterval: null,
      isAutoShooting: false,

      flash: false,
      showSnap: false,
    };
  },

  mounted() {
    this.initCamera();
  },

  beforeDestroy() {
    this.stopAll();
  },

  methods: {
    async initCamera() {
      const stream = await navigator.mediaDevices.getUserMedia({
        video: true,
        audio: false,
      });
      this.$refs.camera.srcObject = stream;
    },

    /* ===== 카운트다운 ===== */
    startCountdown() {
      if (this.isAutoShooting) return;

      this.countdown = 5;

      this.countdownTimer = setInterval(() => {
        this.countdown--;

        if (this.countdown <= 0) {
          clearInterval(this.countdownTimer);
          this.countdown = 0;
          this.startAutoShot();
        }
      }, 1000);
    },

    /* ===== 자동 촬영 ===== */
    startAutoShot() {
      this.isAutoShooting = true;

      this.capture();

      this.autoInterval = setInterval(() => {
        if (this.getImageLen >= 6) {
          this.stopAll();
          return;
        }
        this.capture();
      }, 5000);
    },

    capture() {
      const context = this.$refs.canvas.getContext("2d");
      context.drawImage(this.$refs.camera, 0, 0, 600, 450);

      this.flashEffect();
      this.snapEffect();

      const image = this.$refs.canvas.toDataURL("image/jpeg");
      this.$set(this.images, new Date().getTime(), image);
    },

    flashEffect() {
      this.flash = true;
      setTimeout(() => {
        this.flash = false;
      }, 150);
    },

    snapEffect() {
      this.showSnap = true;
      setTimeout(() => {
        this.showSnap = false;
      }, 700);
    },

    stopAll() {
      this.isAutoShooting = false;

      if (this.autoInterval) clearInterval(this.autoInterval);
      if (this.countdownTimer) clearInterval(this.countdownTimer);

      const tracks = this.$refs.camera?.srcObject?.getTracks();
      tracks?.forEach((t) => t.stop());
    },
  },

  computed: {
    getImageLen() {
      return Object.keys(this.images).length;
    },
  },
};
</script>

<style scoped>
.camera-frame {
  background: black;
  width: 750px;
  height: 450px;
  box-shadow: 1px 1px 3px black;
}

.camera-wrapper {
  position: relative;
  width: 600px;
  height: 450px;
  overflow: hidden;
}

video {
  width: 100%;
  height: 100%;
  object-fit: cover;
}

.countdown-overlay {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  font-size: 150px;
  font-weight: bold;
  color: white;
  z-index: 10;
  animation: pop 1s ease;
}

.snap-text {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  font-size: 100px;
  font-weight: bold;
  color: yellow;
  z-index: 11;
  animation: snapAnim 0.7s ease;
}

.flash {
  position: absolute;
  width: 100%;
  height: 100%;
  background: white;
  opacity: 0.8;
  z-index: 9;
  animation: flashAnim 0.15s ease;
}

.takePic {
  width: 60px;
  height: 60px;
  border-radius: 50%;
  background: white;
  cursor: pointer;
}

.takePic-inner {
  width: 50px;
  height: 50px;
  border-radius: 50%;
  background: white;
  border: 2px solid grey;
}

@keyframes pop {
  0% {
    transform: translate(-50%, -50%) scale(0.5);
    opacity: 0;
  }
  100% {
    transform: translate(-50%, -50%) scale(1);
    opacity: 1;
  }
}

@keyframes flashAnim {
  0% {
    opacity: 0.8;
  }
  100% {
    opacity: 0;
  }
}

@keyframes snapAnim {
  0% {
    transform: translate(-50%, -50%) scale(0.5);
    opacity: 0;
  }
  100% {
    transform: translate(-50%, -50%) scale(1);
    opacity: 1;
  }
}
</style>

<template>
  <div class="step-two">
    <div class="mb-5">
      <div v-if="isLoading" style="font-size: 20px; height: 600px">
        <div class="text-center">
          <div><i class="mdi mdi-loading mdi-spin"></i></div>
          <small>카메라 설정 중</small>
        </div>
      </div>
      <div
        v-else-if="!isLoading && canPhoto"
        class="d-flex justify-content-center align-items-center"
      >
        <div v-if="rows <= columns" class="camera-horizontal camera-frame">
          <div
            class="col-custom h-100 text-center d-flex align-items-center justify-content-center"
            style="width: 50px; color: #fff; font-size: 20px"
          >
            <div v-if="isPhotoTaken">
              마<br />음<br />에<br /><br />들<br />면<br /><br />다<br />음<br />으<br />로<br />!
            </div>
            <div v-else-if="getImageLen < 6">
              {{ 6 - getImageLen
              }}<br />장<br /><br />남<br />았<br />어<br />요<br />!
            </div>
            <div v-else>
              이<br />제<br /><br />꾸<br />미<br />러<br /><br />가<br />볼<br />까<br />요<br />?
            </div>
          </div>
          <div class="col-custom">
            <video
              v-show="!isPhotoTaken"
              ref="camera"
              width="600"
              height="450"
              autoplay
            ></video>
            <canvas
              v-show="isPhotoTaken"
              id="photoTaken"
              ref="canvas"
              width="600"
              height="450"
            ></canvas>
            <div v-if="countdown > 0" class="countdown-overlay">
              {{ countdown }}
            </div>
            <div v-if="flash" class="flash"></div>
          </div>
          <audio ref="shutterSound" src="/sounds/shutter.mp3"></audio>
          <div v-if="!isShotPhoto" class="col-custom" style="width: 100px">
            <div class="w-100">
              <div
                class="d-flex justify-content-center align-items-center"
                style="height: 75px"
              >
                <i
                  v-if="isPhotoTaken"
                  class="mdi mdi-trash-can"
                  style="font-size: 30px; color: #fff"
                  @click="isPhotoTaken = false"
                ></i>
              </div>
              <div
                class="d-flex justify-content-center align-items-center"
                style="height: 300px"
              >
                <div
                  v-if="getImageLen < 6"
                  class="takePic d-flex justify-content-center align-items-center"
                  v-on="
                    isPhotoTaken
                      ? {
                          click: () => {
                            saveImage();
                            isPhotoTaken = false;
                          },
                        }
                      : {
                          click: () => {
                            if (!this.isAutoShooting) {
                              this.startAutoShot();
                            }
                          },
                        }
                  "
                >
                  <div class="takePic-inner">
                    <div
                      v-if="isPhotoTaken"
                      class="h-100 d-flex justify-content-center align-items-center"
                    >
                      <i class="mdi mdi-tray-plus" style="font-size: 20px"></i>
                    </div>
                  </div>
                </div>
                <div
                  v-else-if="getImageLen == 6"
                  class="takePic d-flex justify-content-center align-items-center"
                  style="font-size: 25px"
                >
                  <i class="mdi mdi-restore" @click="isOpen = true"></i>
                </div>
              </div>
              <div style="height: 75px"></div>
            </div>
          </div>
        </div>
        <div v-else class="camera-vertical camera-frame">
          <div
            class="d-flex justify-content-center align-items-center"
            style="height: 50px; color: #fff; font-size: 20px"
          >
            <div v-if="isPhotoTaken">마음에 들면 다음 사진찍어볼까</div>
            <div v-else-if="getImageLen < 6">
              {{ 6 - getImageLen }} 장 남았어요!
            </div>
            <div v-else>이제 꾸미러 가볼까요?</div>
          </div>
          <div>
            <video
              v-show="!isPhotoTaken"
              ref="camera"
              width="450"
              height="600"
              autoplay
            ></video>
            <canvas
              v-show="isPhotoTaken"
              id="photoTaken"
              ref="canvas"
              width="450"
              height="600"
            ></canvas>
            <div v-if="countdown > 0" class="countdown-overlay">
              {{ countdown }}
            </div>
            <div v-if="flash" class="flash"></div>
            <audio ref="shutterSound" src="/sounds/shutter.mp3"></audio>
          <div v-if="!isShotPhoto">
            <div class="d-flex justify-content-center align-items-center">
              <div class="row m-0 p-0 w-100" style="height: 100px">
                <div class="col-2"></div>
                <div
                  class="col-8 d-flex align-items-center justify-content-center"
                >
                  <div
                    v-if="getImageLen == 6"
                    class="takePic d-flex justify-content-center align-items-center"
                    style="font-size: 25px"
                  >
                    <i class="mdi mdi-restore" @click="isOpen = true"></i>
                  </div>
                  <div
                    v-else
                    class="takePic d-flex justify-content-center align-items-center"
                    v-on="
                      isPhotoTaken
                        ? {
                            click: () => {
                              saveImage();
                              isPhotoTaken = false;
                            },
                          }
                        : {
                            click: () => {
                              this.startAutoShot();
                            },
                          }
                    "
                  >
                    <div class="takePic-inner">
                      <div
                        v-if="isPhotoTaken"
                        class="h-100 d-flex justify-content-center align-items-center"
                      >
                        <i
                          class="mdi mdi-tray-plus"
                          style="font-size: 20px"
                        ></i>
                      </div>
                    </div>
                  </div>
                </div>
                <div
                  class="col-2 d-flex align-items-center justify-content-center"
                >
                  <i
                    v-if="isPhotoTaken"
                    class="mdi mdi-trash-can"
                    style="font-size: 30px; color: #fff"
                    @click="isPhotoTaken = false"
                  ></i>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
      <div v-else class="m-auto" style="height: 450px; width: 600px">
        <div v-if="!getImageLen" class="h-100 card" @click="onUploadClick">
          <div class="m-auto">
            <i class="mdi mdi-arrow-up-bold" style="font-size: 70px"></i>
            <div><strong>사진 올리기</strong></div>
          </div>
        </div>
        <div v-else class="m-auto h-100">
          <div class="h-100" style="box-shadow: 1px 1px 3px black">
            <div
              class="m-auto row h-50"
              v-for="(row, rowIdx) of [0, 1]"
              :key="rowIdx"
            >
              <div
                class="col-4 m-auto"
                v-for="(col, colIdx) of [0, 1, 2]"
                :key="colIdx"
              >
                <div
                  v-if="Object.values(images)[rowIdx * 3 + col]"
                  class="card m-auto mb-3"
                  style="position: relative"
                >
                  <img
                    class="uploadImage"
                    :src="`${Object.values(images)[rowIdx * 3 + col]}`"
                  />
                  <div class="overlay">
                    <i
                      class="mdi mdi-close"
                      @click="removeImg(Object.keys(images)[rowIdx * 3 + col])"
                    ></i>
                  </div>
                </div>
                <div v-else class="text-center card" @click="onUploadClick">
                  <i class="mdi mdi-arrow-up-bold" style="font-size: 60px"></i>
                  <div><strong>사진 올리기</strong></div>
                </div>
              </div>
            </div>
          </div>
          <div v-if="getImageLen == 6" class="text-center">
            <button class="btn-custom" @click="initImage">초기화</button>
          </div>
        </div>

        <div class="text-center" v-if="getImageLen < 6">
          카메라가 없다면 가지고 계신 사진을 6장까지 넣어주세요!
        </div>
        <input
          ref="fileInput"
          @change="onImageUpload"
          type="file"
          style="display: none"
          multiple
        />
      </div>
    </div>
    <modal
      v-if="isOpen"
      @on-close="isOpen = false"
      @on-submit="
        isOpen = false;
        initImage();
      "
      :title="'사진 삭제'"
      :msg="'찍은 사진들을 모두 초기화 하시겠어요?'"
    ></modal>
  </div>
</template>

<script>
import Modal from "../../components/modal.vue";

export default {
  name: "StepOne",
  components: { Modal },

  data() {
    return {
      images: {},
      rows: 2,
      columns: 1,
      canPhoto: false,
      isOpen: false,
      isPhotoTaken: false,
      isShotPhoto: false,
      isLoading: false,

      countdownTimer: null,
      countdown: 0,
      isAutoShooting: false,
      flash: false,
    };
  },

  mounted() {
    let table = this.$store.getters.getFrame;
    this.rows = table.split("x")[0];
    this.columns = table.split("x")[1];
    this.images = this.$store.getters.getImages;

    this.$store.commit("setNext", this.getImageLen == 6);

    this.createCameraElement();
  },

  beforeDestroy() {
    this.stopAllShooting();
  },

  methods: {
    /* ================= 카메라 생성 ================= */

    createCameraElement() {
      this.isLoading = true;

      navigator.mediaDevices
        .getUserMedia({ video: true, audio: false })
        .then((stream) => {
          this.isLoading = false;
          this.canPhoto = true;

          // 🔥 DOM 렌더 후 ref 접근
          this.$nextTick(() => {
            if (this.$refs.camera) {
              this.$refs.camera.srcObject = stream;
            }
          });
        })
        .catch((error) => {
          console.error(error);
          this.isLoading = false;
          this.canPhoto = false;
        });
    },

    /* ================= 카운트다운 ================= */

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

    /* ================= 자동촬영 ================= */

    startAutoShot() {
      if (this.getImageLen >= 6) {
        this.stopAllShooting();
        return;
      }

      this.isAutoShooting = true;
      this.countdown = 5;

      this.countdownTimer = setInterval(() => {
        this.countdown--;

        if (this.countdown <= 0) {
          clearInterval(this.countdownTimer);
          this.countdown = 0;

          this.captureAndSave();

          setTimeout(() => {
            this.startAutoShot(); // 다음 장
          }, 1000);
        }
      }, 1000);
    },

    captureAndSave() {
      if (!this.$refs.canvas || !this.$refs.camera) return;

      const context = this.$refs.canvas.getContext("2d");

      if (this.rows <= this.columns) {
        context.drawImage(this.$refs.camera, 0, 0, 600, 450);
      } else {
        context.drawImage(this.$refs.camera, 0, 0, 450, 600);
      }
      this.flash = true;
      setTimeout(() => {
        this.flash = false;
      }, 150);

      /* 🔊 셔터 사운드 */
      if (this.$refs.shutterSound) {
        this.$refs.shutterSound.currentTime = 0;
        this.$refs.shutterSound.play();
      }

      const image = this.$refs.canvas
        .toDataURL("image/jpeg")
        .replace("image/jpeg", "image/octet-stream");

      let id = Date.now();
      this.$set(this.images, id, image);
    },

    stopAllShooting() {
      this.isAutoShooting = false;

      if (this.countdownTimer) {
        clearInterval(this.countdownTimer);
        this.countdownTimer = null;
      }

      this.stopCameraStream();
    },

    stopCameraStream() {
      if (!this.$refs.camera?.srcObject) return;

      this.$refs.camera.srcObject.getTracks().forEach((track) => track.stop());
    },

    initImage() {
      this.stopAllShooting();
      this.$store.commit("setNext", false);
      this.$store.commit("setImages", {});
      this.images = {};
      this.createCameraElement();
    },

    removeImg(target) {
      this.$delete(this.images, target);
      this.$store.commit("setImages", this.images);
    },

    onUploadClick() {
      this.$refs.fileInput.click();
    },

    async onImageUpload(e) {
      if (this.getImageLen >= 6) return;

      let files = Array.from(e.target.files);

      for await (let file of files) {
        if (this.getImageLen >= 6) break;
        let id = Date.now();
        this.$set(this.images, id, await this.readFile(file));
      }
    },

    readFile(file) {
      return new Promise((resolve) => {
        const reader = new FileReader();
        reader.onload = (e) => resolve(e.target.result);
        reader.readAsDataURL(file);
      });
    },
  },

  computed: {
    getImageLen() {
      return Object.keys(this.images).length;
    },
  },

  watch: {
    images: {
      deep: true,
      handler() {
        this.$store.commit("setNext", this.getImageLen == 6);
        this.$store.commit("setImages", this.images);
      },
    },
  },
};
</script>

<style lang="scss" scoped>
.col-custom {
  float: left;
  position: relative; // 👈 이거 추가

  &-50 {
    width: 50px;
  }
  &-100 {
    width: 100px;
  }
  &-600 {
    width: 100px;
  }
}

.camera-frame {
  background-color: black;
}

.camera-horizontal,
.camera-vertical {
  position: relative;
}

.takePic {
  width: 60px;
  height: 60px;
  border-radius: 50%;
  background-color: #fff;
  border: 1px solid black;
}

.takePic-inner {
  width: 50px;
  height: 50px;
  border-radius: 50%;
  background-color: #fff;
  border: 2px solid grey;
}

.uploadImage {
  width: 160px;
  height: 120px;
}

.overlay {
  position: absolute;
  font-size: 30px;
  top: 0%;
  left: 0%;
}

.previewImg-horizontal {
  height: 60;
  width: 80;
}

.previewImg-vertical {
  height: 80;
  width: 60;
}

canvas,
video {
  object-fit: cover;
}
.countdown-overlay {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  font-size: 150px;
  color: white;
  font-weight: bold;
  z-index: 10;
  animation: pop 1s ease;
}

.snap-text {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  font-size: 100px;
  color: yellow;
  font-weight: bold;
  z-index: 11;
  animation: snapAnim 0.7s ease;
}

.camera-wrapper {
  position: relative;
}

.flash {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: white;
  opacity: 0.9;
  pointer-events: none;
  animation: flashAnim 0.15s ease;
}

@keyframes flashAnim {
  0% {
    opacity: 0.9;
  }
  100% {
    opacity: 0;
  }
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

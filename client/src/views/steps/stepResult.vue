<template>
  <div class="step-result">
    <div class="text-center mb-3">
      <button class="btn-custom mr-3" @click="saveResult">저 장 하 기</button>
      <button class="btn-custom" @click="$router.push('/')">다 시 하 기</button>
    </div>
    <div class="mb-5">
      <div
        ref="result"
        :class="`m-auto outter-frame outter-frame-${columns}-${rows}`"
      >
        <result-frame :rows="rows" :columns="columns"></result-frame>
      </div>
    </div>
  </div>
</template>

<script>
import resultFrame from "./frames/resultFrame.vue";
import html2canvas from "html2canvas";

export default {
  name: "step-result",
  components: {
    resultFrame,
  },
  data() {
    return {
      rows: 2,
      columns: 1,
    };
  },
  created() {
    let table = this.$store.getters.getTable;

    this.rows = table.rows;
    this.columns = table.columns;
  },
  methods: {
    async saveResult() {
      const canvas = await html2canvas(this.$refs.result, {
        backgroundColor: "#ffffff",
      });

      const base64 = canvas.toDataURL("image/png");

      // 🔥 Android 앱 안에서 실행 중이면
      if (window.AndroidBridge && window.AndroidBridge.saveImage) {
        window.AndroidBridge.saveImage(base64);
      }
      // 🔥 일반 브라우저일 경우
      else {
        const a = document.createElement("a");
        a.href = base64;
        a.download = "Image.png";
        document.body.appendChild(a);
        a.click();
        document.body.removeChild(a);
      }
    },
  },
};
</script>

<style lang="scss" scoped>
.outter-frame {
  &-1-1 {
    height: 350px;
    width: 400px;
  }
  &-1-2 {
    height: 520px;
    width: 320px;
  }
  &-1-3 {
    height: 525px;
    width: 220px;
  }
  &-1-4 {
    height: 560px;
    width: 180px;
  }
  &-2-1 {
    height: 320px;
    width: 520px;
  }
  &-2-2 {
    height: 520px;
    width: 620px;
  }
  &-2-3 {
    height: 525px;
    width: 420px;
  }
  &-3-1 {
    height: 220px;
    width: 525px;
  }
  &-3-2 {
    height: 420px;
    width: 525px;
  }
  &-4-1 {
    height: 180px;
    width: 560px;
  }
}
</style>

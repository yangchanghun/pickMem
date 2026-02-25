<template>
  <div class="step-result">
    <div class="text-center mb-3">
      <button class="btn-custom mr-3" @click="saveResult">저 장 하 기</button>
      <button class="btn-custom ml-3" @click="printResult">인 쇄 하 기</button>
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
    async printResult() {
      try {
        const canvas = await html2canvas(this.$refs.result, {
          useCORS: true,
          allowTaint: true,
          backgroundColor: "#ffffff",
          scale: 2,
        });

        const base64 = canvas.toDataURL("image/png");

        // 새 창 열기
        const printWindow = window.open("", "_blank");

        printWindow.document.write(`
      <html>
        <head>
          <title>Print</title>
          <style>
            body {
              margin: 0;
              display: flex;
              justify-content: center;
              align-items: center;
              height: 100vh;
              background: white;
            }
            img {
              max-width: 100%;
              max-height: 100%;
            }
            @media print {
              body {
                margin: 0;
              }
            }
          </style>
        </head>
        <body>
          <img src="${base64}" />
          <script>
            window.onload = function() {
              window.print();
              window.close();
            };
          <\/script>
        </body>
      </html>
    `);

        printWindow.document.close();
      } catch (err) {
        console.error(err);
        alert("인쇄 중 오류 발생");
      }
    },

    async saveResult() {
      try {
        console.log("saveResult clicked");

        const canvas = await html2canvas(this.$refs.result, {
          useCORS: true,
          allowTaint: true,
          backgroundColor: "#ffffff",
          scale: 2,
        });

        const base64 = canvas.toDataURL("image/png");

        console.log("Base64 length:", base64.length);

        // 🔥 Android WebView 환경
        if (window.AndroidBridge && window.AndroidBridge.saveImage) {
          console.log("Sending to AndroidBridge...");
          window.AndroidBridge.saveImage(base64);
        }
        // 🔥 일반 브라우저
        else {
          console.log("Browser download mode...");
          const a = document.createElement("a");
          a.href = base64;
          a.download = "Image.png";
          document.body.appendChild(a);
          a.click();
          document.body.removeChild(a);
        }
      } catch (error) {
        console.error("saveResult error:", error);
        alert("저장 중 오류가 발생했습니다.");
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

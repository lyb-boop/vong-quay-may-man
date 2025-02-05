<template>

  <main class="container py-4 my-5">

    <div class="row align-content-start justify-content-around">

      <div class="col-12 text-lg-start">
        <h1 class="mb-4 mb-lg-5 text-center">🎡 VÒNG QUAY MAY MẮN 🎡</h1>
      </div>

      <div class="col-12 col-lg-1 order-1 order-lg-0 mt-5">
      </div>

      <div class="col-12 col-lg-10 px-lg-5 order-0 order-lg-1">

        <div class="sticky-top">

          <div class="fs-2 text-center">
            <div v-if="winnerResult">
              Chúc mừng bạn đã về với đội: <span :style="{ color: winnerResult.color, fontWeight: 'bold' }">{{ winnerResult.text }} </span> 🎉
            </div>
            <div v-else-if="isSpinning">
              Đang quay...
            </div>
            <div v-else>
              Bạn đã sẵn sàng quay chưa?
            </div>
          </div>

          <ShiningDots
              :color="shiningDotsColor"
              :border-color="shiningDotsBorderColor"
              :shine-color="shiningDotsShineColor"
              :border-width="shiningDotsBorderWidth"
              :size="shiningDotsSize"
              :count="shiningDotsCount">

            <VueWheelSpinner
                ref="spinner"
                :slices="slices"
                :winner-index="defaultWinner"
                :spin-duration="spinDuration"
                :sounds="sounds"
                :cursor-angle="cursorAngle"
                :cursor-position="cursorPosition"
                :cursor-distance="cursorDistance"
                @spin-start="onSpinStart"
                @spin-end="onSpinEnd">

              <template #cursor>
                <img class="cursor-img" :src="cursorImage" alt="Cursor">
              </template>

              <template #default>
                <button
                    class="spin-button"
                    :disabled="isSpinning"
                    @click="handleSpinButtonClick"
                    @mouseover="handleSpinButtonHover"
                    @mouseleave="handleSpinButtonLeave">
                  QUAY
                </button>
              </template>

            </VueWheelSpinner>

          </ShiningDots>

          <div>
            <button
                class="btn btn-success btn-lg rounded-pill w-100 py-4"
                :disabled="isSpinning"
                @click="spinRandom()">
                <span class="spinner-border spinner-border-sm me-2" v-if="isSpinning" role="status">
                  <span class="visually-hidden">Loading...</span>
                </span>
              Bắt đầu quay
            </button>
          </div>

        </div>

      </div>

      <div class="col-12 col-lg-1 order-1 order-lg-1 mt-5">
      </div>

    </div>
  </main>


</template>

<script>
import VueWheelSpinner from 'vue-wheel-spinner';
import 'bootstrap/js/src/dropdown.js'

import cursorImage from './assets/cursor.svg';
import wonSound from './sounds/won.mp3';
import clickSound from './sounds/click.mp3';
import hoverSound from './sounds/hover.mp3';
import leaveSound from './sounds/leave.mp3';
import spinningSound from './sounds/spinning.mp3';
import ShiningDots from "@/components/ShiningDots.vue";

export default {
  components: {
    ShiningDots,
    VueWheelSpinner
  },
  data() {
    return {
      winnerResult: null,
      slices: this.createSpiningData(),
      isSpinning: false,
      defaultWinner: 0,
      spinDuration:6000,
      sounds: {
        won: wonSound,
        spinButtonClick: clickSound,
        spinButtonHover: hoverSound,
        spinButtonLeave: leaveSound,
        spinning: spinningSound
      },
      cursorImage,
      cursorAngle: 0,
      cursorPosition: 'edge',
      cursorDistance: 0,
      shiningDotsColor: '#ffffff',
      shiningDotsShineColor: '#ffd800',
      shiningDotsBorderColor: '#1e254c',
      shiningDotsBorderWidth: 30,
      shiningDotsSize: 8,
      shiningDotsCount: 60
    };
  },
  watch: {
    slices: {
      handler() {
        this.$refs.spinner.drawWheel();
      },
      deep: true
    },
    shiningDotsBorderWidth() {
      this.$refs.spinner.drawWheel();
    },
  },
  methods: {
    getRandomColor() {
      return '#' + Math.floor(Math.random() * 16777215).toString(16);
    },
    createSpiningData(){
      return [
        {color: this.getRandomColor(), text:"PC01"},
        {color: this.getRandomColor(), text:"PC02"},
        {color: this.getRandomColor(), text:"PC03"},
        {color: this.getRandomColor(), text:"PC04"},
        {color: this.getRandomColor(), text:"PC06"},
        {color: this.getRandomColor(), text:"PC07"},
        {color: this.getRandomColor(), text:"PC08"},
        {color: this.getRandomColor(), text:"PC09"},
        {color: this.getRandomColor(), text:"PC10"},
        {color: this.getRandomColor(), text:"PC11"},
        {color: this.getRandomColor(), text:"PV01"},
        {color: this.getRandomColor(), text:"PX01"},
        {color: this.getRandomColor(), text:"PX03"},
        {color: this.getRandomColor(), text:"PX05"},
        {color: this.getRandomColor(), text:"PA01"},
        {color: this.getRandomColor(), text:"PA02"},
        {color: this.getRandomColor(), text:"PA03"},
        {color: this.getRandomColor(), text:"PA04"},
        {color: this.getRandomColor(), text:"PA06"},
        {color: this.getRandomColor(), text:"PA09"},
        {color: this.getRandomColor(), text:"Công an Xã"},
        {color: this.getRandomColor(), text:"Công an Phường"},
        {color: this.getRandomColor(), text:"Công an thị trấn"},
      ]
    },
    playAudio(audio) {
      if (audio) {
        audio.volume = 0.5
        audio.play();
      }
    },
    handleSpinButtonClick() {
      if (this.buttonClickAudio) {
        this.playAudio(this.buttonClickAudio)
      }
      this.spinRandom();
    },
    handleSpinButtonHover() {
      if (this.buttonHoverAudio) {
        this.playAudio(this.buttonHoverAudio)
      }
    },
    handleSpinButtonLeave() {
      if (this.buttonLeaveAudio) {
        this.playAudio(this.buttonLeaveAudio)
      }
    },
    handleCursorPositionChange() {
      if (this.cursorPosition === 'center') {
        if (!this.cursorDistance) {
          this.cursorDistance = 50;
        }
      } else {
        this.cursorDistance = 0;
      }
    },
    spinFor(index) {
      this.defaultWinner = index;
      this.$refs.spinner.spinWheel(index);
    },
    spinRandom() {
      const randomSlice = Math.floor(Math.random() * this.slices.length);
      this.$refs.spinner.spinWheel(randomSlice);
    },
    onSpinStart() {
      this.winnerResult = null;
      this.isSpinning = true;
    },
    onSpinEnd(winnerIndex) {
      this.isSpinning = false;
      this.winnerResult = this.slices[winnerIndex];
    }
  },
  mounted() {
    this.buttonHoverAudio = new Audio(hoverSound);
    this.buttonLeaveAudio = new Audio(leaveSound);
    this.buttonClickAudio = new Audio(clickSound);
  }
};
</script>

<style>

.cursor-img {
  width: 50px;
  aspect-ratio: 1 / 1;
  filter: drop-shadow(3px 3px 2px rgba(0, 0, 0, 0.19));
}

.spin-button {
  width: 100px;
  height: 100px;
  margin: 0 auto;
  aspect-ratio: 1 / 1;
  font-size: 20px;
  cursor: pointer;
  background: #eb4d4b;
  border-radius: 50%;
  transition: all 150ms;
  border: 10px solid white;
  display: flex;
  align-items: center;
  justify-content: center;
  font-weight: 600;
  color: white !important;
  box-shadow: inset -3px -3px 2px 2px rgba(0, 0, 0, 0.19), 3px 3px 2px 2px rgba(0, 0, 0, 0.19);
  z-index: 11;
  position: relative;
  user-select: none;

  &:hover {
    box-shadow: inset -5px -5px 2px 2px rgba(0, 0, 0, 0.19), 3px 3px 2px 2px rgba(0, 0, 0, 0.19);
  }

  &:active {
    box-shadow: inset 3px 3px 2px 2px rgba(0, 0, 0, 0.19), 3px 3px 2px 2px rgba(0, 0, 0, 0.19);
  }

  &:disabled {
    background: #ccc;
    cursor: not-allowed;
    pointer-events: none;
  }

}

</style>
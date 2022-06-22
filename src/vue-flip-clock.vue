<template>
  <div class="clock">
    <div
      class="cards-container"
      v-for="(computedClock, index) of computedRealClock"
      :key="index"
    >
      <div class="container">
        <div class="card bg-up">
          <div class="inner">
            <div class="content">
              {{ computedClock.nextFormat.slice(-2, -1) }}
            </div>
          </div>
        </div>
        <div class="card bg-down">
          <div class="inner">
            <div class="content">
              {{ computedClock.currentFormat.slice(-2, -1) }}
            </div>
          </div>
        </div>
        <div
          class="flip card"
          :style="
            computedClock.ifTens
              ? `transform: rotateX(-${computedClock.degree}deg);`
              : ''
          "
        >
          <div class="front inner">
            <div class="content">
              {{ computedClock.currentFormat.slice(-2, -1) }}
            </div>
          </div>
          <div class="back inner">
            <div class="content">
              {{ computedClock.nextFormat.slice(-2, -1) }}
            </div>
          </div>
        </div>
      </div>
      <div class="container">
        <div class="card bg-up">
          <div class="inner">
            <div class="content">
              {{ computedClock.nextFormat.slice(-1) }}
            </div>
          </div>
        </div>
        <div class="card bg-down">
          <div class="inner">
            <div class="content">
              {{ computedClock.currentFormat.slice(-1) }}
            </div>
          </div>
        </div>
        <div
          class="flip card"
          :style="`transform: rotateX(-${computedClock.degree}deg);`"
        >
          <div class="front inner">
            <div class="content">
              {{ computedClock.currentFormat.slice(-1) }}
            </div>
          </div>
          <div class="back inner">
            <div class="content">
              {{ computedClock.nextFormat.slice(-1) }}
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "flip-clock",
  data() {
    return {
      curTime: new Date(0),
      realClock: [
        { max: 23, current: 0, degree: 0 },
        { max: 59, current: 0, degree: 0 },
        { max: 59, current: 0, degree: 0 },
      ],
    };
  },
  computed: {
    computedRealClock() {
      const { realClock } = this;
      return realClock.map((clock) => ({
        ...clock,
        nextFormat: `0${clock.current + 1 > clock.max ? 0 : clock.current + 1}`,
        currentFormat: `0${clock.current}`,
        ifTens:
          parseInt(clock.current / 10) !== parseInt((clock.current + 1) / 10),
      }));
    },
  },
  methods: {
    flip(newVal, index = 2) {
      const clock = this.realClock[index];

      if (clock.degree < 180) {
        clock.degree += 4;
        requestAnimationFrame(() => {
          this.flip(newVal, index);
        });
      } else {
        clock.degree = 0;
        clock.current = newVal;
      }
    },
    timeFlies() {
      const newTime = new Date();

      if (newTime.getHours() !== this.curTime.getHours()) {
        requestAnimationFrame(() => {
          this.flip(newTime.getHours(), 0);
        });
      }

      if (newTime.getMinutes() !== this.curTime.getMinutes()) {
        requestAnimationFrame(() => {
          this.flip(newTime.getMinutes(), 1);
        });
      }

      if (newTime.getSeconds() !== this.curTime.getSeconds()) {
        requestAnimationFrame(() => {
          this.flip(newTime.getSeconds(), 2);
        });
      }

      this.curTime = newTime;
      requestAnimationFrame(this.timeFlies);
    },
  },
  created() {
    this.timeFlies();
  },
};
</script>

<style scoped>
.clock {
  display: flex;
  align-items: center;
  justify-content: center;
}

.cards-container {
  display: flex;
  justify-content: center;
  position: relative;
}

.cards-container + .cards-container {
  margin-left: 20px;
}

.cards-container + .cards-container::before {
  width: 30px;
  text-align: center;
  z-index: 1;
  position: absolute;
  left: -26px;
  top: 50%;
  transform: translateY(-50%);
  content: ":";
  color: white;
  font-weight: bold;
  font-size: 40px;
  text-shadow: 1px 2px 2px rgb(0 0 0 / 30%);
}

.container {
  position: relative;
  width: 30px;
  height: 45px;
  perspective: 200px;
  position: relative;
  border-radius: 4px;
  box-shadow: 0 2px 5px rgb(0 0 0 / 40%);
}

.container::after {
  content: "";
  position: absolute;
  left: 0;
  right: 0;
  height: 1px;
  background: rgb(0 0 0 / 70%);
  box-shadow: 0 1px 2px 0 rgb(0 0 0 / 40%);
  top: calc(50% - 1px);
}

.container + .container {
  margin-left: 5px;
}

.card {
  border-radius: 4px;
  position: absolute;
  font-size: 40px;
  text-align: center;
  line-height: 50px;
  width: 100%;
  height: 50%;
  transform-style: preserve-3d;
  transform-origin: 0% 100%;
}

.inner {
  border-radius: 4px 4px 0 0;
  position: absolute;
  width: 100%;
  height: 100%;
  background: var(--accent-color);
  box-sizing: border-box;
  backface-visibility: hidden;
  overflow: hidden;
}

.inner .content {
  color: white;
  font-weight: bold;
  text-shadow: 1px 2px 2px rgb(34 40 49 / 30%);
  cursor: context-menu;
}

.bg-down {
  top: 50%;
}

.bg-down .inner {
  border-radius: 0 0 4px 4px;
}

.bg-down .inner .content {
  transform: translateY(-48%);
  cursor: context-menu;
}

.back {
  transform: rotateY(180deg);
}

.back .content {
  transform-origin: 50% 47%;
  transform: rotate(180deg);
  cursor: context-menu;
}
</style>

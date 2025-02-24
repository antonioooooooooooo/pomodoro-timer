<template>
  <div class="wrapper" :style="{ backgroundColor }">
    <div class="timer">
      <div class="button-box">
        <button
          class="mode-button"
          :class="{ 'mode-button-selected': mode === WORK }"
          @click="
            mode = WORK;
            remainingTime = INITIAL_TIMES[WORK];
          "
        >
          Work
        </button>
        <button
          class="mode-button"
          :class="{ 'mode-button-selected': mode === SHORT_BREAK }"
          @click="
            mode = SHORT_BREAK;
            remainingTime = INITIAL_TIMES[SHORT_BREAK];
          "
        >
          Short Break
        </button>
        <button
          class="mode-button"
          :class="{ 'mode-button-selected': mode === LONG_BREAK }"
          @click="
            mode = LONG_BREAK;
            remainingTime = INITIAL_TIMES[LONG_BREAK];
          "
        >
          Long Break
        </button>
      </div>
      <div class="remaining-time-text">
        {{ Math.floor(remainingTime / 60) }}:{{
          (remainingTime % 60).toString().padStart(2, "0")
        }}
      </div>
      <div class="action-button-box">
        <button
          @click="toggleTimer"
          class="action-button"
          :class="{ 'pause-button': isRunning }"
          :style="{ color: backgroundColor }"
        >
          {{ isRunning ? "PAUSE" : "START" }}
        </button>
        <button
          v-if="isStarted && !isRunning"
          @click="reset"
          class="restart-button"
        />
      </div>
    </div>
  </div>
</template>

<script>
const WORK = 0,
  SHORT_BREAK = 1,
  LONG_BREAK = 2;

const INITIAL_TIMES = {
  [WORK]: 25 * 60,
  [SHORT_BREAK]: 5 * 60,
  [LONG_BREAK]: 25 * 60,
};

const BACKGROUND_COLORS = {
  [WORK]: "#3db0c2",
  [SHORT_BREAK]: "#c85798",
  [LONG_BREAK]: "#d2960f",
};

const MESSAGES = {
  [WORK]: "Good job! Take a break now, you deserve it.",
  [SHORT_BREAK]:
    "Your break is over, hopefully you feel energized and ready to get back to work!",
  [LONG_BREAK]:
    "Your break is over, hopefully you feel energized and ready to get back to work!",
};

export default {
  data() {
    return {
      mode: WORK,
      remainingTime: INITIAL_TIMES[WORK],
      isRunning: false,
      isStarted: false,
      timer: null,
    };
  },
  computed: {
    backgroundColor() {
      return BACKGROUND_COLORS[this.mode];
    },
  },
  methods: {
    toggleTimer() {
      if (this.isRunning) {
        clearInterval(this.timer);
        this.timer = null;
      } else {
        this.isStarted = true;
        this.timer = setInterval(() => {
          this.remainingTime--;

          if (this.remainingTime === -1) {
            if (Notification.permission === "granted") {
              new Notification("Time's up!", {
                body: MESSAGES[this.mode],
                icon: "https://icons.iconarchive.com/icons/flat-icons.com/flat/256/Clock-icon.png",
              });
            }
            this.reset();
          }
        }, 1000);
      }
      this.isRunning = !this.isRunning;
    },
    reset() {
      if (this.time !== null) {
        clearInterval(this.timer);
        this.timer = null;
      }
      this.remainingTime = INITIAL_TIMES[this.mode];
      this.isRunning = false;
      this.isStarted = false;
    },
  },
  created() {
    this.WORK = WORK;
    this.SHORT_BREAK = SHORT_BREAK;
    this.LONG_BREAK = LONG_BREAK;
    this.INITIAL_TIMES = INITIAL_TIMES;
  },
  mounted() {
    Notification.requestPermission();
  },
};
</script>

<style src="./PomodoroTimer.css" scoped />

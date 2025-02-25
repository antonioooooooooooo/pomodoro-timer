<template>
  <div class="wrapper" :style="{ backgroundColor }">
    <div
      class="edit-length-wrapper"
      :class="{ 'edit-length-wrapper-started': isStarted }"
    >
      <h2 class="edit-length-title">
        Edit {{ mode === WORK ? "session" : "break" }} length:
      </h2>
      <div class="edit-length-inputs-wrapper">
        <input
          class="length-input"
          type="number"
          :disabled="isStarted"
          v-model="minutes"
          @input="handleLengthInput('minutes', $event)"
        />
        :
        <input
          class="length-input"
          type="number"
          :disabled="isStarted"
          v-model="seconds"
          @input="handleLengthInput('seconds', $event)"
        />
      </div>
      <button
        @click="remainingTime = parseInt(minutes) * 60 + parseInt(seconds)"
        class="checkmark-button"
      />
    </div>
    <div class="timer">
      <div class="button-box">
        <button
          class="mode-button"
          :class="{ 'mode-button-selected': mode === WORK }"
          @click="
            mode = WORK;
            remainingTime = INITIAL_TIMES[WORK];
            minutes = Math.floor(INITIAL_TIMES[WORK] / 60)
              .toString()
              .padStart(2, '0');
            seconds = (INITIAL_TIMES[WORK] % 60).toString().padStart(2, '0');
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
            minutes = Math.floor(INITIAL_TIMES[SHORT_BREAK] / 60)
              .toString()
              .padStart(2, '0');
            seconds = (INITIAL_TIMES[SHORT_BREAK] % 60)
              .toString()
              .padStart(2, '0');
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
            minutes = Math.floor(INITIAL_TIMES[LONG_BREAK] / 60)
              .toString()
              .padStart(2, '0');
            seconds = (INITIAL_TIMES[LONG_BREAK] % 60)
              .toString()
              .padStart(2, '0');
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
    <div class="spotify-wrapper">
      <iframe
        style="border-radius: 12px"
        src="https://open.spotify.com/embed/playlist/6X185BlQApNN7mjiFFhPdi?utm_source=generator&theme=0"
        width="100%"
        height="80"
        frameBorder="0"
        allowfullscreen=""
        allow="autoplay; clipboard-write; encrypted-media; fullscreen; picture-in-picture"
        loading="lazy"
      />
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
      minutes: Math.floor(INITIAL_TIMES[WORK] / 60)
        .toString()
        .padStart(2, "0"),
      seconds: (INITIAL_TIMES[WORK] % 60).toString().padStart(2, "0"),
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
    handleLengthInput(key, e) {
      let value = e.target.value.replace(/^0+(\d)$/, "0$1");
      let intValue = parseInt(value, 10);

      if (isNaN(intValue)) {
        this[key] = 0;
        return;
      }

      this[key] = Math.min(59, Math.max(0, intValue))
        .toString()
        .padStart(2, "0");

      e.target.value = this[key] < 10 ? `0${this[key]}` : this[key];
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

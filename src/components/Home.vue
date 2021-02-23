<template>
  <div class="container">
    <div class="title">
      <h1>Pomodoro &#127813;</h1>
    </div>
    <div class="timer">
      <svg
        width="163"
        height="165"
        viewBox="-8 -8 184 188"
        fill="none"
        id="first-segment"
      >
        <path
          stroke="#E9D2B1"
          stroke-width="15"
          stroke-linecap="round"
          d="M165.16,163.38A172,172,0,0,0,0,0"
        />
        <path
          id="top-right"
          stroke="#F85959"
          stroke-width="15"
          stroke-linecap="round"
          d="M165.16,163.38A172,172,0,0,0,0,0"
        />
      </svg>
      <svg
        width="163"
        height="165"
        viewBox="-8 -8 184 188"
        fill="none"
        id="second-segment"
      >
        <path
          stroke="#E9D2B1"
          stroke-width="15"
          stroke-linecap="round"
          d="M0,163.34A172,172,0,0,0,164.44,0"
        />
        <path
          id="bottom-right"
          stroke="#F85959"
          stroke-width="15"
          stroke-linecap="round"
          d="M0,163.34A172,172,0,0,0,164.44,0"
        />
      </svg>
      <svg
        width="163"
        height="165"
        viewBox="-8 -8 184 188"
        fill="none"
        id="third-segment"
      >
        <path
          stroke="#E9D2B1"
          stroke-width="15"
          stroke-linecap="round"
          d="M0,0A172,172,0,0,0,165.16,162.61"
        />
        <path
          id="bottom-left"
          stroke="#F85959"
          stroke-width="15"
          stroke-linecap="round"
          d="M0,0A172,172,0,0,0,165.16,162.61"
        />
      </svg>
      <svg
        width="163"
        height="165"
        viewBox="-8 -8 184 188"
        fill="none"
        id="fourth-segment"
      >
        <path
          stroke="#E9D2B1"
          stroke-width="15"
          stroke-linecap="round"
          d="M160.17,0A172,172,0,0,0,0,161.51"
        />
        <path
          id="top-left"
          stroke="#F85959"
          stroke-width="15"
          stroke-linecap="round"
          d="M160.17,0A172,172,0,0,0,0,161.51"
        />
      </svg>
      <div class="time-display">
        <p v-if="resting">Rest</p>
        <h2>{{ timeDisplay }}</h2>
      </div>
    </div>
    <button @click="handleTimer" :disabled="resting">{{ buttonText }}</button>
  </div>
</template>

<script>
import ProgressBar from "progressbar.js";
import beep from "../assets/beep.mp3";
import confetti from "canvas-confetti";

export default {
  name: "Home",
  data: () => {
    const pomodoroDuration = 25 * 60; // 25 mins to secs

    return {
      pomodoroDuration,
      restDuration: 5 * 60,
      currentTimeInSeconds: pomodoroDuration,
      currentSegment: 1,
      buttonText: "Start!",
      topRight: null,
      bottomRight: null,
      bottomLeft: null,
      topLeft: null,
      pathOptions: {
        easing: "linear",
        duration: (pomodoroDuration + 1) * 1000, // add a sec and convert to millis
      },
      interval: null,
      beepAudio: new Audio(beep),
      resting: false,
    };
  },
  mounted: function() {
    this.topRight = new ProgressBar.Path("#top-right", this.pathOptions);
    this.topRight.set(1);

    this.bottomRight = new ProgressBar.Path("#bottom-right", this.pathOptions);
    this.bottomRight.set(1);

    this.bottomLeft = new ProgressBar.Path("#bottom-left", this.pathOptions);
    this.bottomLeft.set(1);

    this.topLeft = new ProgressBar.Path("#top-left", this.pathOptions);
    this.topLeft.set(1);
  },
  methods: {
    handleTimer() {
      if (this.buttonText === "Start!" || this.buttonText === "Resume") {
        this.animateBar();
        this.buttonText = "Pause";
      } else if (this.buttonText === "Pause") {
        this.pauseBar();
        this.buttonText = "Resume";
      }
    },
    animateBar() {
      this.reduceTime();
      let segment = null;
      switch (this.currentSegment) {
        case 1:
          segment = this.topRight;
          break;
        case 2:
          segment = this.bottomRight;
          break;
        case 3:
          segment = this.bottomLeft;
          break;
        case 4:
          segment = this.topLeft;
          break;
      }
      segment.animate(
        0,
        {
          duration: (this.currentTimeInSeconds + 1) * 1000,
        },
        this.onFinish
      );
    },
    pauseBar() {
      clearInterval(this.interval);
      switch (this.currentSegment) {
        case 1:
          this.topRight.stop();
          break;
        case 2:
          this.bottomRight.stop();
          break;
        case 3:
          this.bottomLeft.stop();
          break;
        case 4:
          this.topLeft.stop();
          break;
      }
    },
    onFinish() {
      if (this.currentTimeInSeconds <= 0) {
        // When finish, we want it to beep for a few seconds then only start rest timer
        if (this.currentSegment < 4) {
          this.currentSegment += 1;
        } else {
          // Reset all
          this.topRight.set(1);
          this.topLeft.set(1);
          this.bottomRight.set(1);
          this.bottomLeft.set(1);

          confetti({
            particleCount: 300,
            spread: 100,
            origin: { y: 0.7 },
          });

          this.currentSegment = 1;
        }
        // Clear interval
        clearInterval(this.interval);

        // Play audio
        this.beepAudio.play();

        // Immediately disable button and set state
        this.resting = true;
        this.buttonText = "Rest";

        setTimeout(() => {
          // Change time to reflect rest duration
          this.currentTimeInSeconds = this.restDuration;

          // Start rest after beep ends
          this.startRest();
        }, 4200);
      }
    },
    reduceTime() {
      this.interval = setInterval(() => {
        if (this.currentTimeInSeconds > 0) {
          this.currentTimeInSeconds -= 1;
        }
      }, 1000);
    },
    startRest() {
      // Set new interval
      this.reduceTime();
      setTimeout(() => {
        clearInterval(this.interval);
        this.beepAudio.play();
        this.currentTimeInSeconds = this.pomodoroDuration;
        this.buttonText = "Start!";
        this.resting = false;
      }, this.restDuration * 1000);
    },
  },
  computed: {
    timeDisplay() {
      const minutes = String(parseInt(this.currentTimeInSeconds / 60));
      const seconds = String(this.currentTimeInSeconds % 60);
      const paddedMinutes = ("0" + minutes).slice(-2);
      const paddedSeconds = ("0" + seconds).slice(-2);

      return `${paddedMinutes}:${paddedSeconds}`;
    },
  },
};
</script>

<style>
.container {
  height: 100vh;
  display: flex;
  flex-direction: column;
  align-items: center;
}

.title {
  margin-top: 50px;
}

h1 {
  color: #c44747;
  font-size: 64px;
}

.timer {
  margin-top: 150px;
  width: 330px;
  height: 330px;
  position: relative;
}

#first-segment {
  position: absolute;
  top: 0px;
  right: 0px;
}

#second-segment {
  position: absolute;
  bottom: 0px;
  right: 0px;
}

#third-segment {
  position: absolute;
  bottom: 0px;
  left: 0px;
}

#fourth-segment {
  position: absolute;
  top: 0px;
  left: 0px;
}

.time-display {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
}

p {
  font-size: 48px;
  line-height: 48px;
  text-align: center;
  color: #ff8080;
}

h2 {
  font-size: 64px;
  color: #f85959;
  text-align: center;
}

button {
  margin-top: 90px;
  width: 200px;
  height: 68px;
  background: #f85959;
  border-radius: 20px;
  border: none;
  cursor: pointer;

  font-size: 36px;
  line-height: 45px;
  text-align: center;
  color: #fff8ee;
}

button:focus {
  outline: none;
}

button:disabled {
  opacity: 0.6;
  cursor: default;
}

canvas {
  background: none;
}
</style>

<template>
  <div class="container">
    <div id="title">
      <h1>Pomodoro</h1>
      <img src="../assets/tomato.png" alt="tomato" width="64px" height="64px" />
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
    <button @click="handleButtonClick">{{ buttonText }}</button>
  </div>
</template>

<script>
import ProgressBar from "progressbar.js";
import beep from "../assets/beep.mp3";

export default {
  name: "Home",
  data: function() {
    const pomodoroDuration = 0.1 * 60;
    return {
      pomodoroDuration: pomodoroDuration,
      restDuration: 0.05 * 60,
      currentTimeInSeconds: pomodoroDuration,
      currentSegment: 1,
      buttonText: "Start!",
      topRight: null,
      bottomRight: null,
      bottomLeft: null,
      topLeft: null,
      pathOptions: {
        easing: "linear",
        duration: (pomodoroDuration + 0.5) * 1000,
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
    handleButtonClick() {
      if (this.buttonText === "Start!" || this.buttonText === "Resume") {
        this.buttonText = "Pause";
        this.animateBar();
      } else if (this.buttonText === "Pause") {
        this.pauseBar();
        this.buttonText = "Resume";
      }
    },
    animateBar() {
      this.interval = setInterval(() => {
        this.currentTimeInSeconds -= 1;
      }, 1000);
      switch (this.currentSegment) {
        case 1:
          this.topRight.animate(0, this.onFinish);
          break;
        case 2:
          this.bottomRight.animate(0, this.onFinish);
          break;
        case 3:
          this.bottomLeft.animate(0, this.onFinish);
          break;
        case 4:
          this.topLeft.animate(0, this.onFinish);
          break;
      }
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
        clearInterval(this.interval);

        if (this.currentSegment < 4) {
          this.currentSegment += 1;
        } else {
          this.currentSegment = 1;
        }

        this.beepAudio.play();

        this.resting = true;

        setTimeout(() => {
          this.buttonText = "Start!";
          this.currentTimeInSeconds = this.restDuration;
        }, 4500);
      }
    },
  },
  computed: {
    timeDisplay() {
      const minutes = parseInt(this.currentTimeInSeconds / 60);
      const seconds = this.currentTimeInSeconds % 60;
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

#title {
  margin-top: 50px;
  display: flex;
  flex-direction: row;
  align-items: center;
}

h1 {
  font-size: 64px;
  color: #c44747;
  margin-right: 10px;
}

.timer {
  position: relative;
  margin-top: 100px;
  width: 330px;
  height: 330px;
}

#first-segment {
  position: absolute;
  top: 0;
  right: 0;
}

#second-segment {
  position: absolute;
  bottom: 0;
  right: 0;
}

#third-segment {
  position: absolute;
  bottom: 0;
  left: 0;
}

#fourth-segment {
  position: absolute;
  top: 0;
  left: 0;
}

button {
  margin-top: 50px;
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

h2 {
  font-size: 64px;
  color: #f85959;
}

p {
  font-size: 48px;
  line-height: 48px;
  text-align: center;
  color: #ff8080;
}
</style>

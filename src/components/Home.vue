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
      <h2>{{ timeDisplay }}</h2>
    </div>
    <button @click="handleTimer">{{ buttonText }}</button>
  </div>
</template>

<script>
import ProgressBar from "progressbar.js";

//TODO:
// Add pause function
// Add on finish function
// Add sound
// Rest
// Next segment
// Restart all
// Confetti on finish all?

export default {
  name: "Home",
  data: () => {
    const pomodoroDuration = 1 * 60 * 1000; // 25 mins to millisecs

    return {
      currentTimeInSeconds: pomodoroDuration / 1000,
      restDuration: 5,
      currentSegment: 1,
      buttonText: "Start!",
      topRight: null,
      bottomRight: null,
      bottomLeft: null,
      topLeft: null,
      pathOptions: {
        easing: "linear",
        duration: pomodoroDuration,
      },
      interval: null,
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
      } else {
        this.buttonText = "Start!";
      }
    },
    animateBar() {
      this.interval = setInterval(() => {
        if (this.currentTimeInSeconds > 0) {
          this.currentTimeInSeconds -= 1;
        }
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
    },
    onFinish() {
      console.log("Finish");
      if (this.currentSegment < 4) {
        this.currentSegment += 1;
      } else {
        this.currentSegment = 1;
      }
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

h2 {
  position: absolute;
  font-size: 64px;
  color: #f85959;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
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
</style>

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
      <h2>25:00</h2>
    </div>
    <button @click="handleTimer">{{ buttonText }}</button>
  </div>
</template>

<script>
import ProgressBar from "progressbar.js";

export default {
  name: "Home",
  mounted: function() {
    var topRight = new ProgressBar.Path("#top-right", {
      easing: "easeOut",
      duration: this.pomodoroDuration * 60 * 1000,
    });
    topRight.set(1);

    var bottomRight = new ProgressBar.Path("#bottom-right", {
      easing: "easeOut",
      duration: this.pomodoroDuration * 60 * 1000,
    });
    bottomRight.set(1);

    var bottomLeft = new ProgressBar.Path("#bottom-left", {
      easing: "easeOut",
      duration: this.pomodoroDuration * 60 * 1000,
    });
    bottomLeft.set(1);

    var topLeft = new ProgressBar.Path("#top-left", {
      easing: "easeOut",
      duration: this.pomodoroDuration * 60 * 1000,
    });
    topLeft.set(1);
  },
  data: () => {
    return {
      pomodoroDuration: 25,
      restDuration: 5,
      currentSegment: 1,
      buttonText: "Start!",
      topRight: null,
      bottomRight: null,
      bottomLeft: null,
      topLeft: null,
    };
  },
  methods: {
    handleTimer() {
      if (this.buttonText === "Start!" || this.buttonText === "Resume") {
        this.buttonText = "Pause";
      } else if (this.buttonText === "Pause") {
        this.buttonText = "Resume";
      } else {
        this.buttonText = "Start!";
      }
      this.animateBar();
    },
    animateBar() {
      switch (this.currentSegment) {
        case 1:
          this;
      }
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

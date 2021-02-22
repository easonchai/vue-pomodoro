<template>
  <div class="container">
    <div class="title">
      <h1>Pomodoro &#127813;</h1>
    </div>
    <div class="timer">
      <svg width="163" height="165" viewBox="-8 -8 184 188" fill="none">
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
    </div>
  </div>
</template>

<script>
export default {
  name: "Home",
  mounted: function() {
    var ProgressBar = require("progressbar.js");
    var bar = new ProgressBar.Path("#top-right", {
      easing: "easeOut",
      duration: 2400,
    });

    bar.set(1);
    bar.animate(0);
  },
};
</script>

<style>
.container {
  height: 100vh;
  display: flex;
  justify-content: center;
  position: relative;
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
}
</style>

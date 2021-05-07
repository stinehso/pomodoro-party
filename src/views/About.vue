<template>
  <div class="about">
    <h1>This is the timer page!</h1>
    <BaseTimer :time-left="timeLeft" :time-limit="timeLimit" />
  </div>
</template>

<script>
import BaseTimer from "@/components/BaseTimer.vue";

export default {
  name: "About",
  components: {
    BaseTimer
  },
  data() {
    return {
      timeLimit: 20,
      timePassed: 0,
      timerInterval: null
    };
  },
  mounted() {
    this.startTimer()
  },
  watch: {
    timeLeft(newValue) {
      if (newValue === 0) {
        this.onTimesUp();
      }
    }
  },
  computed: {
    timeLeft() {
      return this.timeLimit - this.timePassed;
    }
  },
  methods: {
    startTimer() {
      this.timerInterval = setInterval(() => (this.timePassed += 1), 1000);
    },
    onTimesUp() {
      clearInterval(this.timerInterval);
    }
  }
};
</script>

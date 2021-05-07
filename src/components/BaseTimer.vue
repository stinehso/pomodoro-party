<template>
  <div class="base-timer">
    <!-- <div class="pie ten"></div> -->
    <svg
      class="base-timer svg"
      viewBox="0 0 100 100"
      xmlns="http://www.w3.org/2000/svg"
    >
      <g class="base-timer circle">
        <circle class="base-timer path-elapsed" cx="50" cy="50" r="46.5" />
        <path
          class="base-timer path-remaining"
          :stroke-dasharray="circleDasharray"
          :class="remainingPathColor"
          d="
            M 50, 50
            m -45, 0
            a 45,45 0 1,0 90,0
            a 45,45 0 1,0 -90,0
          "
        ></path>
      </g>
    </svg>
    <span class="base-timer label">
      {{ formattedTimeLeft }}
    </span>
  </div>
</template>

<script>
export default {
  props: {
    timeLeft: {
      type: Number,
      required: true
    },
    timeLimit: {
      type: Number,
      required: true
    },
    alertThreshold: {
      type: Number,
      default: 5
    },
    warningThreshold: {
      type: Number,
      default: 10
    }
  },
  data() {
    return {
      circumference: 283
    };
  },
  computed: {
    formattedTimeLeft() {
      const timeLeft = this.timeLeft;
      // The largest round integer less than or equal to the result of time divided being by 60.
      const minutes = Math.floor(timeLeft / 60);
      // Seconds are the remainder of the time divided by 60 (modulus operator)
      let seconds = timeLeft % 60;
      // If the value of seconds is less than 10, then display seconds with a leading zero
      if (seconds < 10) {
        seconds = `0${seconds}`;
      }
      // The output in MM:SS format
      return `${minutes}:${seconds}`;
    },
    // Update the dasharray value as time passes, starting with 283
    circleDasharray() {
      return `${(this.timeFraction * this.circumference).toFixed(0)} 283`;
    },
    timeFraction() {
      // Divides time left by the defined time limit.
      const rawTimeFraction = this.timeLeft / this.timeLimit;
      // Subtract one timeunitfraction times what starts at almost 0 and approaches 1
      return rawTimeFraction - (1 / this.timeLimit) * (1 - rawTimeFraction);
    },
    colorCodes() {
      return {
        info: {
          color: "green"
        },
        warning: {
          color: "orange",
          threshold: this.warningThreshold
        },
        alert: {
          color: "red",
          threshold: this.alertThreshold
        }
      };
    },
    remainingPathColor() {
      const { alert, warning, info } = this.colorCodes;
      if (this.timeLeft <= alert.threshold) {
        return alert.color;
      } else if (this.timeLeft <= warning.threshold) {
        return warning.color;
      } else {
        return info.color;
      }
    }
  }
};
</script>

<style scoped lang="css">
.base-timer {
  position: relative;
  width: 300px;
  height: 300px;
  margin-left: auto;
  margin-right: auto;
}

.circle {
  fill: none;
  stroke: none;
}

.path-elapsed {
  stroke-width: 7px;
  stroke: grey;
}

.path-remaining {
  /* Just as thick as the original ring */
  stroke-width: 7px;
  /* Rounds the line endings to create a seamless circle */
  stroke-linecap: round;
  /* Makes sure the animation starts at the top of the circle */
  transform: rotate(90deg);
  transform-origin: center;
  /* One second aligns with the speed of the countdown timer */
  transition: 1s linear all;
  /* Allows the ring to change color when the color value updates */
  fill-rule: nonzero;
  stroke: currentColor;
}

.green {
  color: rgb(65, 184, 131);
}
.orange {
  color: orange;
}
.red {
  color: red;
}

/* .svg {
  /* Flips the svg and makes the animation to move left-to-right
  transform: scaleX(-1);
  } */

.label {
  position: absolute;

  /* Size should match the parent container */
  width: 300px;
  height: 300px;
  /* Keep the label aligned to the top */
  top: 0;
  /* Create a flexible box that centers content vertically and horizontally */
  display: flex;
  align-items: center;
  justify-content: center;
  /* Sort of an arbitrary number; adjust to your liking */
  font-size: 48px;
}

.pie {
  margin-left: auto;
  margin-right: auto;
  border-radius: 50%;
  background-color: green;
  height: 10vh;
  width: 10vh;
}

.ten {
  background-image: /* 10% = 126deg = 90 + ( 360 * .1 ) */ linear-gradient(
      196deg,
      transparent 50%,
      white 50%
    ),
    linear-gradient(90deg, white 50%, transparent 50%);
}
</style>

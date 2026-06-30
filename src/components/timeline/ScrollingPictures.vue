<template>
  <div>scrolling</div>
</template>

<script lang="ts">
import { defineComponent } from "vue";
import { END_YEAR, START_YEAR } from "../../consts/data/timeline.ts";
import { events } from "../../consts/data/events";

console.log(START_YEAR, END_YEAR, events);

// This frame contains the percentage of things that should be visible in the window
const windowContainsPercentage = 10;

const totalMonths = (END_YEAR - START_YEAR + 1) * 12;
const totalMonthsVisible = (windowContainsPercentage / 100) * totalMonths;

// Observe the vertical scroll position of the window and calculate what an event should be showing

export default defineComponent({
  name: "ScrollingPictures",
  components: {},
  data() {
    return {};
  },
});
</script>

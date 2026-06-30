<template>
  <div class="scrolling-pictures">
    <div class="scrolling-frame">
      <div class="scrolling-track">
        <SingleEvent
          v-for="event in visibleEvents"
          :key="event.title"
          :event="event"
          :left="event.left"
          :top="event.top"
          :opacity="event.opacity"
          :active="event.active"
        />
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent } from "vue";
import { END_YEAR, START_YEAR } from "../../consts/data/timeline.ts";
import { events } from "../../consts/data/events";
import SingleEvent from "./scrolling/SingleEvent.vue";
import type { TimelineEvent } from "../../types/events";

const windowContainsPercentage = 10;
const totalMonths = (END_YEAR - START_YEAR + 1) * 12;
const lastMonthIndex = totalMonths - 1;
const eventWidthPercent = 30;
const topSafePadding = 8;
const bottomSafePadding = 8;
const safeVerticalRange = 100 - topSafePadding - bottomSafePadding;

function parseDateToMonthIndex(date: string) {
  const [day, month, year] = date.split("/").map(Number);
  return (year - START_YEAR) * 12 + (month - 1);
}

function dayOfMonthOffset(date: string) {
  const [day] = date.split("/").map(Number);
  return topSafePadding + (day / 31) * safeVerticalRange;
}

export default defineComponent({
  name: "ScrollingPictures",
  components: {
    SingleEvent,
  },
  data() {
    return {
      scrollPercentage: 0,
      eventStates: events.map((event) => {
        const monthIndex = parseDateToMonthIndex(event.date);
        const percent =
          lastMonthIndex > 0 ? (monthIndex / lastMonthIndex) * 100 : 0;
        return {
          ...event,
          percent,
          top: dayOfMonthOffset(event.date),
        };
      }) as Array<TimelineEvent & { percent: number; top: number }>,
    };
  },
  computed: {
    visibleEvents() {
      const frameStart =
        this.scrollPercentage * (100 - windowContainsPercentage);

      return this.eventStates.map((event) => {
        const left =
          ((event.percent - frameStart) / windowContainsPercentage) * 100;
        const right = left + eventWidthPercent;
        const visibleLeft = Math.max(left, 0);
        const visibleRight = Math.min(right, 100);
        const visibleWidth = Math.max(0, visibleRight - visibleLeft);
        const opacity = Math.min(1, visibleWidth / eventWidthPercent);
        const active = visibleWidth > 0;
        return {
          ...event,
          active,
          left,
          opacity,
        };
      });
    },
  },
  mounted() {
    this.updateScrollPercentage();
    window.addEventListener("scroll", this.updateScrollPercentage, {
      passive: true,
    });
  },
  unmounted() {
    window.removeEventListener("scroll", this.updateScrollPercentage);
  },
  methods: {
    updateScrollPercentage() {
      const scrollRange =
        document.documentElement.scrollHeight - window.innerHeight;
      const percentage = scrollRange > 0 ? window.scrollY / scrollRange : 0;
      this.scrollPercentage = Math.min(Math.max(percentage, 0), 1);
    },
  },
});
</script>

<style scoped lang="scss">
.scrolling-pictures {
  position: fixed;
  top: 0;
  height: 100%;
  width: calc(100% - 96px);
}

.scrolling-frame {
  position: relative;
  width: 100%;
  height: calc(100% - 96px);
}

.scrolling-track {
  position: relative;
  width: 100%;
  height: 100%;
}
</style>

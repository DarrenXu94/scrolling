<template>
  <div class="year-step-timeline">
    <div class="timeline-track">
      <template v-for="year in years" :key="year">
        <div class="timeline-year">
          <div class="timeline-notch timeline-notch--major">
            <span class="timeline-label">{{ year.year }}</span>
          </div>
          <div class="timeline-subnotches">
            <div
              v-for="sub in 4"
              :key="`${year.year}-${sub}`"
              class="timeline-notch timeline-notch--minor"
            />
          </div>
          <EventDots :events="year.events" />
        </div>
      </template>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent } from "vue";
import { events } from "../../consts/data/events";
import EventDots from "./EventDots.vue";
import { END_YEAR, START_YEAR } from "../../consts/data/timeline.ts";

export default defineComponent({
  name: "YearStep",
  components: {
    EventDots,
  },
  data() {
    return {
      years: Array.from({ length: END_YEAR - START_YEAR + 1 }, (_, i) => ({
        year: START_YEAR + i,
        events: events.filter((event) => {
          const [day, month, year] = event.date.split("/");
          const dateObject = new Date(
            parseFloat(year),
            parseFloat(month) - 1,
            parseFloat(day),
          );

          return dateObject.getFullYear() === START_YEAR + i;
        }),
      })),
    };
  },
});
</script>

<style lang="scss" scoped>
.year-step-timeline {
  width: 100%;
  // overflow-x: auto;
  display: flex;
  align-items: center;
  height: 100%;
}

.timeline-track {
  display: flex;
  justify-content: space-between;
  width: 100%;
  height: 100%;
  align-items: flex-end;
}

.timeline-year {
  width: 100%;
  position: relative;
  height: 100%;
}

.timeline-subnotches {
  display: flex;
  align-items: flex-end;
  width: 100%;
  height: 100%;
  justify-content: space-between;
}

.timeline-notch {
  width: 2px;
  background: #555;
}

.timeline-notch--major {
  position: absolute;
  height: 32px;
  bottom: 0;
}

.timeline-notch--minor {
  height: 16px;
  bottom: 0;
  &:last-child {
    height: 32px;
  }
}

.timeline-label {
  display: block;
  margin-top: 0.35rem;
  font-size: 0.85rem;
  color: #333;
  text-align: center;
  padding-left: 0.5rem;
}
</style>

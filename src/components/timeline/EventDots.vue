<template>
  <div class="event-dots-container">
    <div class="event-dots">
      <div v-for="event in events" :key="event.title">
        <div
          class="event-dot"
          :style="{
            left: dotOffset(event) + '%',
            top: `calc(${dotTopOffsetDayOfMonth(event)}% - 8px)`,
          }"
        ></div>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent } from "vue";
import type { TimelineEvent } from "../../types/events";

export default defineComponent({
  name: "EventDots",
  components: {},
  props: {
    events: {
      type: Array as () => Array<TimelineEvent>,
      required: true,
    },
  },
  data() {
    return {};
  },
  methods: {
    dotOffset(event: TimelineEvent) {
      const eventDate = this.convertStringToDate(event.date);
      const yearStart = new Date(eventDate.getFullYear(), 0, 1);
      const yearEnd = new Date(eventDate.getFullYear() + 1, 0, 1);
      const yearDuration = yearEnd.getTime() - yearStart.getTime();
      const eventOffset = eventDate.getTime() - yearStart.getTime();
      return (eventOffset / yearDuration) * 100;
    },
    dotTopOffsetDayOfMonth(event: TimelineEvent) {
      const eventDate = this.convertStringToDate(event.date);
      const dayOfMonth = eventDate.getDate();
      return (dayOfMonth / 31) * 100;
    },
    convertStringToDate(dateString: string): Date {
      const [day, month, year] = dateString.split("/");
      return new Date(parseFloat(year), parseFloat(month) - 1, parseFloat(day));
    },
  },
});
</script>
<style lang="scss" scoped>
.event-dots-container {
  position: absolute;
  top: 0;
  width: 100%;
  height: 100%;
}
.event-dots {
  position: relative;
  width: 100%;
  height: 100%;
}
.event-dot {
  width: 8px;
  height: 8px;
  border-radius: 50%;
  background-color: #00000096;
  margin: 4px 0;
  position: absolute;
}
</style>

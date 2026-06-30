<template>
  <div
    class="single-event"
    :class="{ 'single-event--active': active }"
    :style="{ left: `${left}%`, top: `${top}%`, opacity }"
  >
    <div class="event-card">
      <div class="event-title">{{ event.title }}</div>
      <div class="event-date">{{ event.date }}</div>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent, type PropType } from "vue";
import type { TimelineEvent } from "../../../types/events";

export default defineComponent({
  name: "SingleEvent",
  props: {
    event: {
      type: Object as PropType<TimelineEvent>,
      required: true,
    },
    left: {
      type: Number,
      required: true,
    },
    top: {
      type: Number,
      required: true,
    },
    opacity: {
      type: Number,
      required: true,
    },
    active: {
      type: Boolean,
      required: true,
    },
  },
});
</script>

<style scoped lang="scss">
.single-event {
  position: absolute;
  transform: translateY(0);
  width: 30%;
  max-width: 260px;
  opacity: 0;
  transition:
    left 0.2s ease,
    top 0.2s ease,
    opacity 0.25s ease;
  pointer-events: none;
}

.single-event--active {
  opacity: 1;
  pointer-events: auto;
}

.event-card {
  background: rgba(33, 33, 33, 0.9);
  color: #fff;
  padding: 1rem;
  border-radius: 16px;
  box-shadow: 0 18px 40px rgba(0, 0, 0, 0.18);
}

.event-title {
  font-weight: 700;
  margin-bottom: 0.5rem;
}

.event-date {
  color: #cfcfcf;
  font-size: 0.95rem;
}
</style>

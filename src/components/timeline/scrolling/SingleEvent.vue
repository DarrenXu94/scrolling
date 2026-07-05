<template>
  <div
    class="single-event"
    :class="{ 'single-event--active': active }"
    :style="{ left: `${left}%`, top: `${top}%`, opacity }"
  >
    <div class="event-card">
      <div class="event-title">{{ event.title }}</div>
      <div class="event-date">{{ event.date }}</div>
      <div class="event-images">
        <img
          v-for="pics in event.pics"
          :src="`/images/events/${pics}`"
          :alt="event.title"
          class="event-image"
        />
      </div>
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
  z-index: 1;
  transform: translateY(0);
  width: 30%;
  max-width: 260px;
  opacity: 0;
  transition:
    left 0.2s ease,
    top 0.2s ease,
    all 0.25s ease;
  pointer-events: none;
  &:hover {
    transform: translateY(-5px);
    z-index: 10;
    opacity: 1 !important;
    .event-card {
      background-color: rgba(33, 33, 33, 1);
    }
  }
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

.event-image {
  display: block;
  width: 100%;
  max-width: 100%;
  height: auto;
  object-fit: cover;
  border-radius: 12px;
  margin-top: 1rem;
}

.event-images {
  display: flex;
  flex-wrap: wrap;
  gap: 0.75rem;
  margin-top: 1rem;
}

.event-image {
  flex: 1 1 30%;
  min-width: 0;
  width: 100%;
  max-width: 100%;
  height: auto;
  object-fit: cover;
  border-radius: 12px;
}
</style>

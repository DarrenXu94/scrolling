<template>
  <div class="timeline">
    <div id="draggable-frame"></div>
    <YearStep />
  </div>
</template>

<script lang="ts">
import { defineComponent } from "vue";
import debounce from "lodash.debounce";
import YearStep from "./YearStep.vue";

export default defineComponent({
  name: "Timeline",
  components: {
    YearStep,
  },
  emits: ["drag-percentage"],
  data() {
    return {
      handleDebouncedScroll: null as unknown as (event: Event) => void,
    };
  },
  mounted() {
    const element = document.getElementById("draggable-frame");
    if (element) {
      this.initDragElement(element);
    }

    this.handleDebouncedScroll = debounce(this.handleScroll, 15);
    window.addEventListener("scroll", this.handleDebouncedScroll);
  },
  unmounted() {
    window.removeEventListener("scroll", this.handleDebouncedScroll);
  },
  methods: {
    getScrollRange() {
      return Math.max(
        document.documentElement.scrollHeight - window.innerHeight,
        0,
      );
    },
    getScrollPercentage() {
      const scrollRange = this.getScrollRange();
      if (scrollRange === 0) return 0;

      return window.scrollY / scrollRange;
    },
    getDragRatio(element: HTMLElement) {
      const parentRect = element.parentElement?.getBoundingClientRect();
      const childRect = element.getBoundingClientRect();

      if (!parentRect) return 0;

      const currentLeftOffset = childRect.left - parentRect.left;
      const maxSlidingDistance = parentRect.width - childRect.width;

      if (maxSlidingDistance <= 0) return 0;

      return Math.min(Math.max(currentLeftOffset / maxSlidingDistance, 0), 1);
    },
    updateDraggableFramePosition(element: HTMLElement, percentage: number) {
      const parentRect = element.parentElement?.getBoundingClientRect();
      if (!parentRect) return;

      const maxLeft = Math.max(parentRect.width - element.offsetWidth, 0);
      element.style.left = `${percentage * maxLeft}px`;
    },
    emitDragPercentage(element: HTMLElement) {
      this.$emit("drag-percentage", this.getDragRatio(element).toFixed(2));
    },
    handleScroll() {
      const percentageDownPage = this.getScrollPercentage();

      this.$emit("drag-percentage", percentageDownPage.toFixed(2));

      const draggableFrame = document.getElementById("draggable-frame");
      if (draggableFrame instanceof HTMLElement) {
        this.updateDraggableFramePosition(draggableFrame, percentageDownPage);
      }
    },
    movePageOnDrag(element: HTMLElement) {
      const ratio = this.getDragRatio(element);
      window.scrollTo(0, ratio * this.getScrollRange());
    },
    initDragElement(element: HTMLElement) {
      const dragMouseDown = (e: MouseEvent) => {
        e = e || window.event;
        e.preventDefault();
        // get the mouse cursor position at startup:
        pos3 = e.clientX;
        pos4 = e.clientY;
        document.onmouseup = closeDragElement;
        // call a function whenever the cursor moves:
        document.onmousemove = elementDrag;
      };

      let pos1 = 0,
        pos2 = 0,
        pos3 = 0,
        pos4 = 0;
      element.onmousedown = dragMouseDown;

      const emitDragPercentage = () => {
        this.emitDragPercentage(element);
      };

      // Create a debounced version that waits 300ms after drag stops
      const debouncedEmit = debounce(emitDragPercentage, 300);

      const elementDrag = (e: MouseEvent) => {
        e = e || window.event;
        e.preventDefault();
        const parentRect = element.parentElement?.getBoundingClientRect();
        const parentOffSetLeft = parentRect?.left ?? 0;
        const parentOffSetRight = parentRect?.right ?? window.innerWidth;

        if (e.clientX < parentOffSetLeft || e.clientX > parentOffSetRight) {
          return; // Prevent dragging outside the parent container
        }

        // calculate the new cursor position:
        pos1 = pos3 - e.clientX;
        // pos2 = pos4 - e.clientY;
        pos3 = e.clientX;
        pos4 = e.clientY;
        // set the element's new position:
        // element.style.top = element.offsetTop - pos2 + "px";

        const nextLeft = element.offsetLeft - pos1;
        const minLeft = parentOffSetLeft;
        const maxLeft = parentOffSetRight - element.offsetWidth;

        if (nextLeft < minLeft) {
          element.style.left = `${minLeft}px`;
          return;
        }
        if (nextLeft > maxLeft) {
          element.style.left = `${maxLeft}px`;
          return;
        }

        element.style.left = `${nextLeft}px`;

        this.movePageOnDrag(element);
        // Call the debounced function during drag
        debouncedEmit();
      };

      const closeDragElement = () => {
        // stop moving when mouse button is released:
        document.onmouseup = null;
        document.onmousemove = null;
        // Flush the debounced function to ensure it runs immediately
        debouncedEmit.flush();
      };
    },
  },
});
</script>
<style lang="scss" scoped>
#draggable-frame {
  position: absolute;
  z-index: 9;
  width: 10%;
  height: calc(100% - 12px);
  display: flex;
  align-items: center;
  justify-content: center;
  color: #222;
  font-weight: 600;
  text-align: center;
  cursor: grab;

  /* outer frame border */
  border: 6px solid #2b2b2b7d;
  border-radius: 6px;

  /* subtle bevel and depth */

  /* inner "mat" to give a framed look */
  // padding: 10px;
}
</style>

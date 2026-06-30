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
    handleScroll(event: Event) {
      const percentageDownPage =
        window.scrollY /
        (document.documentElement.scrollHeight - window.innerHeight);

      this.$emit("drag-percentage", percentageDownPage.toFixed(2));

      // update draggable frame position based on scroll
      const draggableFrame = document.getElementById("draggable-frame");
      if (draggableFrame) {
        const parentRect =
          draggableFrame.parentElement!.getBoundingClientRect();
        const newLeft =
          percentageDownPage * (parentRect.width - draggableFrame.offsetWidth);
        draggableFrame.style.left = `${newLeft}px`;
      }
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
        const parentRect = element.parentElement!.getBoundingClientRect();
        const childRect = element.getBoundingClientRect();

        // Calculate how far the child is from the left edge of the parent
        const currentLeftOffset = childRect.left - parentRect.left;

        // Calculate the maximum distance the child can slide to the right
        const maxSlidingDistance = parentRect.width - childRect.width;

        // Prevent division by zero if both divs are the exact same width
        if (maxSlidingDistance === 0) return 0;

        // Return a bound value clamped between 0 and 1
        const ratio = currentLeftOffset / maxSlidingDistance;
        const percentage = Math.min(Math.max(ratio, 0), 1).toFixed(2);

        console.log("Drag percentage:", percentage);
        this.$emit("drag-percentage", percentage);
      };

      // Create a debounced version that waits 300ms after drag stops
      const debouncedEmit = debounce(emitDragPercentage, 300);

      const elementDrag = (e: MouseEvent) => {
        e = e || window.event;
        e.preventDefault();
        const parentOffSetLeft = element.parentElement
          ? element.parentElement.getBoundingClientRect().left
          : 0;

        const parentOffSetRight = element.parentElement
          ? element.parentElement.getBoundingClientRect().right
          : window.innerWidth;

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

        // limit the range to within the screen
        if (element.offsetLeft - pos1 < parentOffSetLeft) {
          element.style.left = parentOffSetLeft + "px";
          return;
        }
        if (
          element.offsetLeft - pos1 >
          parentOffSetRight - element.offsetWidth
        ) {
          element.style.left = parentOffSetRight - element.offsetWidth + "px";
          return;
        }

        element.style.left = element.offsetLeft - pos1 + "px";

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

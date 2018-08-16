<script>
import { EventBus } from "../main";

export default {
  name: "Container",
  inject: ["sandboxState"],
  props: {
    dropzone: {
      default: false,
      type: Boolean
    },
    items: {
      default: () => [],
      type: Array
    },
    dropLimit: {
      default: 4,
      type: Number
    },
    addItem: {
      default: undefined,
      type: Function
    },
    removeItem: {
      default: undefined,
      type: Function
    }
  },
  data() {
    return {
      overContainer: null,
      sourceContainer: null,
      relativeMousePosition: { x: 0, y: 0 }
    };
  },
  computed: {
    dropLimitReached() {
      // I need a better way to do this so I can break the coupling
      let count = this.items.filter(item => {
        if (item["type"] !== undefined && item.type === "dummy") {
          return false;
        }

        return true;
      }).length;

      return this.dropLimit === 0 ? false : count >= this.dropLimit;
    },
    isValidDropZone() {
      return (
        this.$el === this.overContainer &&
        this.$el !== this.sourceContainer &&
        this.dropzone
      );
    },
    canDrop() {
      return this.isValidDropZone && !this.dropLimitReached;
    },
    shouldPrepend() {
      return (
        this.isValidDropZone &&
        (this.relativeMousePosition.y >= 0 && this.relativeMousePosition.y <= 2)
      );
    }
  },
  render(createElement) {
    return this.$scopedSlots.default({
      items: this.items,
      removeItem: this.removeItem,
      prepending: this.shouldPrepend,
      dropLimit: this.dropLimit
    });
  },
  beforeDestroy() {
    this.sandboxState.draggable.removeContainer(this.$el);
  },
  mounted() {
    this.sandboxState.draggable.addContainer(this.$el);

    EventBus.$on("drag:over:container", e => {
      this.overContainer = e.overContainer;
      this.sourceContainer = e.sourceContainer;
    })
      .$on("drag:move", e => {
        this.relativeMousePosition = {
          x: e.sensorEvent.clientX - this.$el.getBoundingClientRect().left,
          y: e.sensorEvent.clientY - this.$el.getBoundingClientRect().top
        };
      })
      .$on("drag:out:container", e => {
        this.overContainer = null;
        this.sourceContainer = null;
      })
      .$on("drag:stop", e => {
        if (this.isValidDropZone) {
          EventBus.$emit("drop:start", {
            source: e.originalSource,
            container: this.overContainer
          });
        } else {
          EventBus.$emit("drop:canceled", {
            source: e.originalSource,
            container: e.overContainer,
            items: this.items,
            mutate: this.removeItem
          });

          if (
            e.overContainer === this.$el &&
            this.dropzone &&
            e.sourceContainer !== this.$el
          ) {
            this.$emit("drop-canceled", this.items);
          }
        }

        this.overContainer = null;
        this.sourceContainer = null;
      })
      .$on("dropping", e => {
        if (this.shouldPrepend) {
          this.$emit("prepend", { items: this.items, item: e.item });
        } else if (this.canDrop) {
          this.$emit("drop", { items: this.items, item: e.item });
        }
      });
  }
};
</script>

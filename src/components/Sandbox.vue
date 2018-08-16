<script>
import { Draggable } from "@shopify/draggable";
import { EventBus } from "../main";

export default {
  name: "Sandbox",
  data() {
    return {
      draggable: new Draggable()
    };
  },
  provide() {
    return {
      sandboxState: {
        draggable: this.draggable
      }
    };
  },
  beforeDestroy() {
    this.draggable.destroy();
  },
  mounted() {
    this.draggable
      .on("drag:start", e => {
        EventBus.$emit("drag:start", e);
      })
      .on("drag:move", e => {
        EventBus.$emit("drag:move", e);
      })
      .on("drag:over", e => {
        EventBus.$emit("drag:over", e);
      })
      .on("drag:over:container", e => {
        EventBus.$emit("drag:over:container", e);
      })
      .on("drag:out", e => {
        EventBus.$emit("drag:out", e);
      })
      .on("drag:out:container", e => {
        EventBus.$emit("drag:out:container", e);
      })
      .on("drag:stop", e => {
        EventBus.$emit("drag:stop", e);
      })
      .on("drag:pressure", e => {
        EventBus.$emit("drag:pressure", e);
      });
  },
  render(createElement) {
    return createElement("div", {
      attrs: {
        class: 'sandbox'
      }
    }, this.$slots.default);
  }
};
</script>

<script>
import { EventBus } from "../main";
export default {
  name: "Item",
  inject: ["sandboxState"],
  props: {
    itemClass: {
      default: "draggable-source",
      type: String
    },
    static: {
      default: false,
      type: Boolean
    },
    itemData: {
      default: null
    }
  },
  render(createElement) {
    return this.$slots.default[0];
  },
  mounted() {
    this.$el.classList.add(this.itemClass);

    EventBus.$on("drag:start", e => {
      if (this.static && e.originalSource === this.$el) {
        e.cancel();
      }
    }).$on("drag:over", e => {
      if (e.originalSource === this.$el) {
        this.$emit("over-item", e);
       }
       
      if (e.over === this.$el) {
        this.$emit("item-enter", e);
       }
    }).$on("drag:out", e => {
      if (e.originalSource === this.$el) {
        this.$emit("out-item", e);
      }

      if (e.over === this.$el) {
        this.$emit("item-leave", e);
      }
    }).$on("drop:start", ({source, container}) => {
      if (this.$el === source && this.itemData) {
        EventBus.$emit('dropping', {item: this.itemData, source, container});
      }
    });

    let itemClass = `.${this.itemClass}`;
    let selectors = this.sandboxState.draggable.options["draggable"].split(",");

    //if we already set our itemClass skip
    if (selectors.indexOf(itemClass) > -1) {
      return;
    }

    selectors.push(itemClass);

    this.sandboxState.draggable.options["draggable"] = selectors.join(",");
  }
};
</script>

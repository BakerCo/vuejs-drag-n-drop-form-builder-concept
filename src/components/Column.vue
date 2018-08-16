<template>
  <item static @item-enter="showDummy" @item-leave="hideDummy">
    <div class="column">
      <span class="dummy" v-if="isDummy"></span>
      <form-item v-bind="data" v-else/>
      <a href="#" class="close pos-top-right" @click.prevent="remove" v-if="!isDummy">×</a>
    </div>                  
  </item>
</template>

<script>
import FormItem from "./FormItem";
import Item from "./Item";

export default {
  components: { FormItem, Item },
  props: {
    data: { required: true },
    id: { required: true }
  },
  methods: {
    remove() {
      this.$emit("remove", this.id);
    },
    showDummy() {
      this.$emit("show-dummy", this.id);
    },
    hideDummy() {
      this.$emit("hide-dummy", this.id);
    }
  },
  computed: {
    isDummy() {
      return this.data.type === "dummy";
    }
  }
};
</script>

<style scoped>
.column {
  flex: 1;
  margin: 0;
  padding: 15px 15px 5px;
  position: relative;
}

.column:hover {
  background-color: #fffff6;
  border-radius: 4px;
}

.column:hover .pos-top-right {
  display: inline;
}

.pos-top-right {
  position: absolute;
  top: -2px;
  right: 2px;
  display: none;
}

.close:hover {
  color: red;
}

.dummy {
  display: block;
  border: 2px dashed #e2e200;
  border-radius: 4px;
  height: 100%;
}
</style>

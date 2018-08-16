<template>
  <container dropzone :items="data.columns" @drop="addColumn" @drop-canceled="hideDummy" @prepend="prepend">
    <div :class="{'flex-row': true, prepend: prepending}" slot-scope="{items: columns, dropLimit, prepending}">
      <column v-for="(col, c) in columns" :key="c" :data="col" :id="c" @remove="removeColumn" @show-dummy="showDummy($event, dropLimit)" @hide-dummy="hideDummy" />
    </div>
  </container>
</template>

<script>
import Column from "./Column";
import Container from "./Container";

export default {
  name: "Row",
  components: { Column, Container },
  props: {
    data: { required: true },
    id: { required: true }
  },
  methods: {
    addColumn({ item, items }) {
      if (this.hasDummy(items)) {
        this.replaceDummyWith(item, items);
        return;
      }

      items.push(item);
    },
    removeColumn(id) {
      this.data.columns.splice(id, 1);
      if (!this.data.columns.length) {
        this.$emit("empty", this.id);
      }
    },
    hasDummy(items) {
      let hasDummy = false;
      items.forEach(column => {
        if (column.type === "dummy") {
          hasDummy = true;
        }
      });

      return hasDummy;
    },
    showDummy(id, dropLimit) {
      if (dropLimit === this.data.columns.length) {
        return;
      }

      this.data.columns.splice(id, 0, { type: "dummy" });
    },
    hideDummy(id) {
      if (!this.hasDummy(this.data.columns)) {
        return;
      }

      if (id === undefined) {
        this.data.columns.forEach((column, index) => {
          if (column.type === "dummy") {
            this.data.columns.splice(index, 1);
          }
        });
        return;
      }

      this.data.columns.splice(id, 1);
    },
    replaceDummyWith(item, items) {
      items.forEach((column, index) => {
        if (column.type === "dummy") {
          items.splice(index, 1, item);
        }
      });
    },
    prepend(e) {
      this.$emit("prepend-row", { id: this.id, ...e });
    }
  }
};
</script>


<style scoped>
.flex-row {
  display: flex;
  margin: 0;
  padding: 0;
  border-top: 5px dashed transparent;
}

.prepend {
  border-top-color: #e2e200;
}
</style>

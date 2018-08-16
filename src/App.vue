<template>
  <div id="app">
    <sandbox>
    
      <div class="container">
        <div class="col-sm-2">
          <container :items="controls">
            <ul class="list-group" slot-scope="{items: controls}">
              <item v-for="(control, i) in controls" :key="i" :item-data="control.data"><li class="list-group-item">{{ control.name }}</li></item>
            </ul>
          </container>
        </div>

        <div class="col-sm-10">
          <container dropzone :items="rows" :drop-limit="0" @drop="addRow">
            <div class="drop-container" slot-scope="{ items: rows }">
              <row v-for="(row, r) in rows" :key="r" :data="row" :id="r" @empty="removeRow" @prepend-row="prependRow"/>
            </div>
          </container>
        </div>

        <div class="col-sm-2 hidden">
          <div class="well well-sm text-center">Properties panel</div>
        </div>
        
        <!--pre class="col-sm-12">{{rows}}</pre-->
      </div>
      
    </sandbox>

  </div>
</template>

<script>
import Sandbox from "./components/Sandbox";
import Container from "./components/Container";
import Item from "./components/Item";
import Row from "./components/Row";

export default {
  name: "App",
  components: {
    Sandbox,
    Container,
    Item,
    Row
  },
  data() {
    return {
      rows: [],
      controls: [
        {
          name: "Textbox",
          data: {
            el: "input",
            type: "text",
            name: "textbox_1",
            placeholder: "Textbox 1",
            label: "Textbox Label"
          }
        },
        {
          name: "Select",
          data: {
            el: "select",
            name: "select_1",
            label: "Select Label",
            options: [{ value: "one", label: "One" }]
          }
        }
      ]
    };
  },
  methods: {
    addRow({ item, items }) {
      items.push({
        columns: [item]
      });
    },
    removeRow(id) {
      this.rows.splice(id, 1);
    },
    prependRow({ id, item, items }) {
      this.rows.splice(id, 0, {
        columns: [item]
      });
    }
  }
};
</script>

<style>
.draggable-mirror {
  z-index: 99999999;
}

#app {
  margin-top: 32px;
}

body {
  background-color: #f9f9f9;
}

.form-group {
  margin: 0;
}

.drop-container {
  background-color: white;
  padding: 10px;
  border-left: 1px solid #dfdfdf;
  min-height: 256px;
}
</style>

<template>
  <div id="app" class="container-fluid">
    <div class="large-12 medium-12 small-12 cell" id="app">
      <input type="file" accept="application/json" @change="onFileChange">
    </div>
    <div class="col-md-9 panel panel-default">
      <tree
        ref="tree"
        :identifier="getId"
        :zoomable="zoomable"
        :data="Graph.tree"
        :node-text="nodeText"
        :margin-x="Marginx"
        :margin-y="Marginy"
        :radius="radius"
        :type="type"
        :layout-type="layoutType"
        :duration="duration"
        class="tree"
        @clicked="onClick"
        @expand="onExpand"
        @retract="onRetract"
      />
    </div>
  </div>
</template>

<script>
import { tree } from "vued3tree";
import axios from "axios";
import data from "../../data/data";
import FileReader from "./components/FileReader";

Object.assign(data, {
  type: "tree",
  duration: 750,
  Marginx: 30,
  Marginy: 30,
  radius: 3,
  nodeText: "text",
  currentNode: null,
  zoomable: true,
  isLoading: false,
  events: []
});

export default {
  name: "app",
  data() {
    return data;
  },
  components: {
    tree,
    FileReader
  },
  methods: {
    getId(node) {
      return node.id;
    },
    onClick(evt) {
      this.currentNode = evt.element;
      this.onEvent("onClick", evt);
    },
    onExpand(evt) {
      this.onEvent("onExpand", evt);
    },
    onRetract(evt) {
      this.onEvent("onRetract", evt);
    },
    onEvent(eventName, data) {
      this.events.push({ eventName, data: data.data });
    }
  },

  onFileChange(e) {
    let files = e.target.files || e.dataTransfer.files;
    if (!files.length) return;
    this.readFile(files[0]);
  },

  readFile(file) {
    let reader = new FileReader();
    reader.onload = e => {
      console.log(e.target.result);
      let json = JSON.parse(e.target.result);
    };
    reader.readAsText(file);
  },

  async mounted() {
    axios
      .get("test.json", {
        headers: {
          "Content-Type": "application/json"
        }
      })
      .then(response => {
        // JSON responses are automatically parsed.
        this.data = response.data;
        // console.log(response.data);
      })
      .catch(e => {
        console.log("error" + e);
        this.errors.push(e);
      });

    fetch("new1.json")
      .then(function(response) {
        if (response.status !== 200) {
          console.log(
            "Looks like there was a problem. Status Code: " + response.status
          );
          return;
        }

        // Examine the text in the response
        response.json().then(function(data) {
          //console.log(data);
        });
      })
      .catch(function(err) {
        console.log("Fetch Error :-S", err);
      });
  }
};
</script>

<style>
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 20px;
}

.tree {
  height: 800px;
  width: 800px;
}

.graph-root {
  height: 800px;
  width: 100%;
}

.feedback {
  height: 50px;
  line-height: 50px;
  vertical-align: middle;
}

.log {
  height: 200px;
  overflow-x: auto;
  overflow-y: auto;
  overflow: auto;
  text-align: left;
}
</style>

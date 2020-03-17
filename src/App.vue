<template>
  <div id="app">
    <div class="h-16 border-b flex items-center">{{config.name}}</div>
    <waterfall :line-gap="winWidth" :watch="items">
      <waterfall-slot
        v-for="(item, index) in items"
        :width="item.width"
        :height="item.height"
        :order="item.index"
        :key="index"
      >
        <div class="p-2">
          <div class="border rounded p-1">
            <div>{{item.name}}</div>
            <div v-html="item.content"></div>
            <div>
              <a
                href="https://raw.githubusercontent.com/Ace520/assets/master/sheet/"
                target="_brack"
              >在GitHub上编辑</a>
            </div>
          </div>
        </div>
      </waterfall-slot>
    </waterfall>
  </div>
</template>

<script>
import Waterfall from "vue-waterfall/lib/waterfall";
import WaterfallSlot from "vue-waterfall/lib/waterfall-slot";
import axios from "axios";
export default {
  name: "App",
  components: {
    Waterfall,
    WaterfallSlot
  },
  data() {
    return {
      rawUrl: "https://raw.githubusercontent.com/Ace520/assets/master/sheet/",
      config: {},
      items: [],
      winWidth: 0,
      content: "",
      md: {}
    };
  },
  mounted() {
    let id = "laravel";
    this.config = require("../static/config/" + id + ".json");
    this.initItems(this.config.items);
    this.winWidth = this.getWidth();
    let MarkdownIt = require("markdown-it");
    this.md = new MarkdownIt();
  },
  methods: {
    initItems(val) {
      for (let i = 0; i < val.length; i++) {
        axios
          .get(this.rawUrl + val[i].path)
          .then(res => {
            this.content = res.data;

            this.items.push({
              width: this.winWidth,
              height: this.getHeight(res.data),
              index: i,
              content: this.md.render(res.data)
            });
          })
          .catch(error => {
            console.error(error);
          });
      }
    },
    getHeight(html) {
      let div = document.createElement("div");
      div.innerText = html;
      document.body.appendChild(div);
      let h = div.scrollHeight;
      div.remove();
      return h;
    },
    getWidth() {
      let w = document.body.clientWidth;
      if (w < 350) {
        return w;
      }
      return w / parseInt(w / 350);
    }
  }
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
  min-height: 150vh;
}
</style>

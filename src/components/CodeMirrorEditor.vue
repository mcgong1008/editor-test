<template>
  <div>
    <el-col :span="4">
      <el-form size="small" label-width="100px">
        <el-form-item label="语言:">
          <el-select v-model="modeVal" @change="handleChangeMode">
            <el-option
              v-for="item in modeOptions"
              :key="item.label"
              :label="item.label"
              :value="item.value"
            >
            </el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="主题:">
          <el-select
            v-model="themeVal"
            @change="handleChangeOption($event, 'theme')"
          >
            <el-option
              v-for="item in themeOptions"
              :key="item.label"
              :label="item.label"
              :value="item.value"
            >
            </el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="字体大小:">
          <el-input
            type="number"
            min="12"
            v-model="fontsizeVal"
            @change="handleChangeFontSize"
          />
        </el-form-item>
        <el-form-item label="显示行号:">
          <el-switch
            v-model="islineNumbers"
            @change="handleChangeOption($event, 'lineNumbers')"
          />
        </el-form-item>
        <el-form-item label="只读:">
          <el-switch
            v-model="readOnly"
            @change="handleChangeOption($event, 'readOnly')"
          />
        </el-form-item>
        <el-form-item label="Diff:">
          <el-switch v-model="diff" @change="handleChangeDiff" />
        </el-form-item>
      </el-form>
    </el-col>
    <el-col :span="20">
      <textarea v-show="!diff" ref="editor"></textarea>
      <div v-show="diff" id="view"></div>
    </el-col>
  </div>
</template>

<script>
import { content } from "../content.js";
import CodeMirror from "codemirror/lib/codemirror.js";
import "codemirror/lib/codemirror.css";
import "codemirror/theme/ambiance.css";
import "codemirror/theme/material.css";
import "codemirror/theme/twilight.css";
import "codemirror/mode/javascript/javascript.js";
import "codemirror/mode/htmlmixed/htmlmixed.js";

import "codemirror/addon/lint/lint.css";
import "codemirror/addon/lint/lint.js";
import "codemirror/addon/lint/javascript-lint.js";
import "codemirror/addon/lint/json-lint.js";
import "codemirror/addon/hint/show-hint.js";
import "codemirror/addon/hint/show-hint.css";
import "codemirror/addon/hint/javascript-hint.js";
import "codemirror/addon/hint/html-hint.js";
import "codemirror/addon/hint/xml-hint.js";
import "codemirror/addon/merge/merge.css";
import "codemirror/addon/merge/merge.js";

export default {
  name: "CodeMirrorEditor",
  data() {
    return {
      editor: null,
      diffEditor: null,
      modeVal: "text/javascript",
      themeVal: "default",
      fontsizeVal: 12,
      islineNumbers: true,
      readOnly: false,
      diff: false,
      modeOptions: [
        {
          label: "javascript",
          value: "text/javascript",
        },
        {
          label: "json",
          value: "application/json",
        },
        {
          label: "html",
          value: "text/html",
        },
      ],
      themeOptions: [
        {
          label: "default",
          value: "default",
        },
        {
          label: "ambiance",
          value: "ambiance",
        },
        {
          label: "material",
          value: "material",
        },
        {
          label: "twilight",
          value: "twilight",
        },
      ],
    };
  },
  mounted() {
    const _this = this;
    this.editor = CodeMirror.fromTextArea(this.$refs.editor, {
      mode: "text/javascript",
      lineNumbers: true,
      gutters: ["CodeMirror-lint-markers"],
      lint: true,
      extraKeys: { "Ctrl-Enter": "autocomplete", "Cmd-Enter": "autocomplete" },
    });
    CodeMirror.commands.autocomplete = function (cm) {
      let hintVal = null;
      if (_this.modeVal === "text/javascript") {
        hintVal = CodeMirror.hint.javascript;
      } else if (_this.modeVal === "text/html") {
        hintVal = CodeMirror.hint.html;
      }
      cm.showHint({ hint: hintVal });
    };
    this.editor.setValue(content[this.modeVal.split("/")[1]]);
    document.getElementsByClassName("CodeMirror")[0].style.fontSize = "12px";
    this.editor.refresh();
  },
  methods: {
    handleChangeMode(mode) {
      this.editor.setOption("mode", mode);
      this.editor.setOption("value", content[this.modeVal.split("/")[1]]);
    },
    handleChangeFontSize(size) {
      document.getElementsByClassName("CodeMirror")[0].style.fontSize =
        size + "px";
      this.editor.refresh();
    },
    handleChangeOption(value, name) {
      this.editor.setOption(name, value);
    },
    handleChangeDiff(val) {
      if (val) {
        const orig1 = `import Vue from 'vue'
import App from './App.vue'\n

Vue.config.productionTip = false

new Vue({
  render: h => h(App),
}).$mount('#app')`;

        const orig2 = `import Vue from "vue";
import Vuex from "vuex";
import App from "./App.vue";
import ElementUI from "element-ui";
import "element-ui/lib/theme-chalk/index.css";

Vue.config.productionTip = false;

Vue.use(ElementUI);
Vue.use(Vuex);

const store = new Vuex.Store({
  state: {
    tabName: "",
  },
  mutations: {
    changeTab(state, tabName) {
      state.tabName = tabName;
    },
  },
});

new Vue({
  render: (h) => h(App),
  store: store,
}).$mount("#app");`;

        document.querySelector(".CodeMirror").remove();
        this.$nextTick(() => {
          this.diffEditor = CodeMirror.MergeView(
            document.getElementById("view"),
            {
              value: orig1,
              origLeft: null,
              orig: orig2,
              lineNumbers: true,
              mode: "text/javascript",
              highlightDifferences: true,
              connect: true,
              collapseIdentical: false,
              // gutters: ["CodeMirror-lint-markers"],
              // lint: true,
            }
          );
          // let d = document.createElement("div");
          // d.style.cssText = "width: 50px; margin: 7px; height: 14px";
          // this.diffEditor.editor().addLineWidget(57, d);
        });
      }
    },
  },
};
</script>
<style>
.CodeMirror {
  border: 1px solid #eee;
  height: 600px;
  margin-left: 10px;
}
</style>
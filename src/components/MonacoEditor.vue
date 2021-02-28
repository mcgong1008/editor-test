<template>
  <div class="container">
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
          <el-select v-model="themeVal" @change="handleChangeTheme">
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
            @change="handleChangeOption(+$event, 'fontSize')"
          />
        </el-form-item>
        <el-form-item label="显示行号:">
          <el-switch
            v-model="islineNumbers"
            @change="handleChangeOption($event, 'lineNumbers')"
          />
        </el-form-item>
        <el-form-item label="高亮激活行:">
          <el-radio-group
            v-model="renderLineHighlight"
            @change="handleChangeOption($event, 'renderLineHighlight')"
          >
            <el-radio label="all">高亮行和边槽</el-radio>
            <el-radio label="line">高亮行</el-radio>
            <el-radio label="gutter">高亮边槽</el-radio>
            <el-radio label="none">不高亮</el-radio>
          </el-radio-group>
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
      <div id="monaco-editor"></div>
    </el-col>
  </div>
</template>

<script>
import { content } from "../content.js";
import * as monaco from "monaco-editor";

export default {
  name: "MonacoEditor",
  data() {
    return {
      editor: null,
      diffEditor: null,
      modeVal: "javascript",
      themeVal: "vs",
      fontsizeVal: 12,
      islineNumbers: true,
      renderLineHighlight: "all",
      readOnly: false,
      diff: false,
      modeOptions: [
        {
          label: "javascript",
          value: "javascript",
        },
        {
          label: "json",
          value: "json",
        },
        {
          label: "html",
          value: "html",
        },
      ],
      themeOptions: [
        {
          label: "vs",
          value: "vs",
        },
        {
          label: "vs-dark",
          value: "vs-dark",
        },
        {
          label: "hc-black",
          value: "hc-black",
        },
      ],
    };
  },
  computed: {
    curEditor() {
      return this.$store.state.tabName;
    },
  },
  watch: {
    curEditor(val) {
      if (val === "monaco" && !this.editor) {
        this.editor = monaco.editor.create(
          document.getElementById("monaco-editor"),
          {
            value: "function hello() {\n\talert('Hello world!');\n}",
            language: "javascript",
            renderLineHighlight: "all",
            // minimap: {
            //   enabled: false,
            // },
            // fontSize: 14,
            // fontWeight: '400',
            // lineNumbers: 'off',
            // tabSize: 2
            // readOnly: true
          }
        );
      }
    },
  },
  methods: {
    handleChangeMode(mode) {
      const model = this.editor.getModel();
      monaco.editor.setModelLanguage(model, mode);
      model.setValue(content[this.modeVal]);
    },
    handleChangeTheme(theme) {
      monaco.editor.setTheme(theme);
    },
    handleChangeOption(value, name) {
      this.editor.updateOptions({
        [name]: value,
      });
    },
    handleChangeDiff(val) {
      if (val) {
        this.editor.getModel().dispose();
        const originalModel = monaco.editor.createModel(
          `import Vue from 'vue'
import App from './App.vue'\n

Vue.config.productionTip = false

new Vue({
  render: h => h(App),
}).$mount('#app')`,
          "javascript"
        );
        const modifiedModel = monaco.editor.createModel(
          `import Vue from "vue";
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
}).$mount("#app");`,
          "javascript"
        );
        this.diffEditor = monaco.editor.createDiffEditor(
          document.getElementById("monaco-editor")
        );
        this.diffEditor.setModel({
          original: originalModel,
          modified: modifiedModel,
        });
      }
    },
  },
};
</script>
<style scoped>
#monaco-editor {
  margin-left: 10px;
  width: 100%;
  height: 450px;
  border: 1px solid #eee;
}
.container >>> .el-radio {
  margin-top: 10px;
}
</style>
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
            @change="handleChangeOption($event, 'showLineNumbers')"
          />
        </el-form-item>
        <el-form-item label="高亮激活行:">
          <el-switch
            v-model="isHighlightActiveLine"
            @change="handleChangeOption($event, 'highlightActiveLine')"
          />
        </el-form-item>
        <el-form-item label="显示边槽:">
          <el-switch
            v-model="isGutter"
            @change="handleChangeOption($event, 'showGutter')"
          />
        </el-form-item>
        <el-form-item label="只读:">
          <el-switch
            v-model="readOnly"
            @change="handleChangeOption($event, 'readOnly')"
          />
        </el-form-item>
        <el-form-item label="提示&补全:">
          <el-switch
            v-model="enableBasicAutocompletion"
            @change="handleChangeAutoCompletion"
          />
        </el-form-item>
      </el-form>
    </el-col>
    <el-col :span="20">
      <div id="ace-editor"></div>
    </el-col>
  </div>
</template>

<script>
import { content } from "../content.js";
import ace from "ace-builds";
import "ace-builds/webpack-resolver";
// import "ace-builds/src-noconflict/mode-javascript";
// import "ace-builds/src-noconflict/ext-language_tools";

export default {
  name: "AceEditor",
  data() {
    return {
      aceEditor: null,
      modeVal: "javascript",
      themeVal: "textmate",
      fontsizeVal: 12,
      islineNumbers: true,
      isHighlightActiveLine: true,
      isGutter: true,
      readOnly: false,
      enableBasicAutocompletion: false,
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
          label: "TextMate",
          value: "textmate",
        },
        {
          label: "Tomorrow",
          value: "tomorrow",
        },
        {
          label: "Tomorrow Night",
          value: "tomorrow_night",
        },
        {
          label: "Monokai",
          value: "monokai",
        },
      ],
    };
  },
  mounted() {
    const _this = this;
    // this.$nextTick(() => {
      _this.aceEditor = ace.edit(document.getElementById("ace-editor"), {
        value: content[_this.modeVal],
        theme: "ace/theme/" + _this.themeVal,
        mode: "ace/mode/" + _this.modeVal,
        minLines: 20,
        maxLines: 20,
      });
    // });
  },
  methods: {
    handleChangeMode(mode) {
      this.aceEditor.setOptions({
        mode: "ace/mode/" + mode,
        value: content[this.modeVal],
      });
      // this.aceEditor.setOption("mode", "ace/mode/" + mode)
      // this.aceEditor.setOption("value", content[this.modeVal])
      // this.aceEditor.setMode("ace/mode/" + mode)
    },
    handleChangeTheme(theme) {
      this.aceEditor.setTheme("ace/theme/" + theme);
    },
    handleChangeOption(value, name) {
      this.aceEditor.setOption(name, value);
    },
    handleChangeAutoCompletion(val) {
      this.aceEditor.setOptions({
        enableBasicAutocompletion: val,
        enableSnippets: val,
        enableLiveAutocompletion: val,
      });
    },
  },
};
</script>

<style scoped>
#ace-editor {
  margin-left: 10px;
  border: 1px solid #eee;
}
</style>

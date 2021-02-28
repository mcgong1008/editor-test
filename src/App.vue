<template>
  <div id="app">
    <!-- <img alt="Vue logo" src="./assets/logo.png">
    <HelloWorld msg="Welcome to Your Vue.js App"/> -->
    <el-tabs tabPosition="left" v-model="activeName" @tab-click="handleChangeTab">
      <el-tab-pane label="CodeMirror" name="codemirror">
        <code-mirror-editor />
      </el-tab-pane>
      <el-tab-pane label="Ace" name="ace">
        <ace-editor />
      </el-tab-pane>
      <el-tab-pane label="Monaco" name="monaco">
        <monaco-editor />
      </el-tab-pane>
    </el-tabs>
  </div>
</template>

<script>
// import HelloWorld from './components/HelloWorld.vue'
import AceEditor from "./components/AceEditor.vue";
import CodeMirrorEditor from "./components/CodeMirrorEditor.vue";
import MonacoEditor from "./components/MonacoEditor.vue";

export default {
  name: "App",
  components: {
    // HelloWorld,
    AceEditor,
    CodeMirrorEditor,
    MonacoEditor,
  },
  data() {
    return {
      activeName: "codemirror",
    };
  },
  methods: {
    handleChangeTab(tab) {
      this.$store.commit("changeTab", tab.name)
    }
  }
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  /* text-align: center; */
  color: #2c3e50;
  margin-top: 60px;
}
</style>

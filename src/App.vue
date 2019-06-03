<template>
  <div id="app" style="padding:15px;">
    <h3>简单使用</h3>
    <vue-mention v-model="text" :list="members1"></vue-mention>
    <h3>自定义插槽</h3>
    <vue-mention v-model="text" :list="members2" valName="val" :config="config">
      <template slot="item" slot-scope="props">
        <b>{{ props.item.val }}</b>
      </template>
    </vue-mention>
    <h3>内容</h3>
    <div style="white-space: pre;">{{text}}</div>
  </div>
</template>

<script>
import VueMention from "./components/VueMention/";

export default {
  components: { VueMention },
  data() {
    return {
      text: "Hello World! @Cyber Fei \n@mxsg.com @小明 ahaha",
      members1: ["Cyber Fei", "mxsg.com", "小明"],
      members2: [
        {
          key: "key1",
          val: "Cyber Fei"
        },
        {
          key: "key2",
          val: "mxsg.com"
        },
        {
          key: "key3",
          val: "小明"
        }
      ],
      config: {
        placeholder: "输入 @ 试下",
        rows: 6
      }
    };
  }
};
</script>

<style>
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
  line-height: 1.6;
}

.vm-editor {
  width: 500px;
  line-height: 1.6;
}
.vm-editor:focus {
  border: 1px solid rgb(2, 117, 216);
  box-shadow: 0 0 0 3px rgb(2, 117, 216, 0.2);
}
</style>

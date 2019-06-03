<template>
  <div class="vm-panel">
    <textarea
      style="resize:none"
      class="vm-editor"
      spellcheck="false"
      v-model="value2"
      @input="handleInput"
      @keyup="keyup"
      @keydown="keydown"
      @blur="showFlag=false"
      v-bind="config"
    ></textarea>

    <partList
      v-show="showFlag"
      ref="list"
      :list="list"
      :top="top"
      :pos="pos"
      :taW="el.offsetWidth"
      @sel="sel"
      :kw="kw"
      :getValue="getValue"
      :valName="valName"
    >
      <template slot="item" slot-scope="props">
        <slot name="item" :item="props.item"></slot>
      </template>
    </partList>
  </div>
</template>

<script>
import partList from "./List";
import "./textarea-caret";

export default {
  components: { partList },
  props: {
    // 绑定数据
    value: String,
    // 候选列表
    list: {
      type: Array,
      default: []
    },
    // 向上打开
    top: {
      type: Boolean,
      default: false
    },
    // 显示字段名
    valName: {
      type: String,
      default: ""
    },
    // 原生配置
    config: {
      type: Object,
      default: () => {
        return {
          placeholder: "请输入...",
          rows: 3
        };
      }
    }
  },
  data() {
    return {
      el: {},
      value2: "",
      pos: { top: -100000, left: 0 },
      showFlag: false,
      kw: ""
    };
  },
  methods: {
    // 获取item的value
    getValue(item) {
      return this.valName ? item[this.valName] : item;
    },

    // 输入
    handleInput(e) {
      if (e.inputType) {
        this.$emit("input", this.value2);
        if (e.data == "@") this.show();
        if (this.showFlag) {
          this.$nextTick(() => {
            let pos = this.el.selectionEnd;
            let str1 = this.value.slice(0, pos);
            let atPos = str1.lastIndexOf("@");
            this.kw = str1.slice(atPos + 1);
          });
        }
      }
    },

    // 设置光标位置
    setRange(pos) {
      this.$nextTick(() => {
        this.el.setSelectionRange(pos, pos);
      });
    },

    // 计算列表位置
    calcPos() {
      let pos = getCaretCoordinates(this.el, this.el.selectionEnd);
      pos.top -= this.el.scrollTop;
      this.pos = pos;
    },
    keyup(e) {
      if (this.showFlag) this.calcPos();
    },

    // 打开列表
    show() {
      this.$refs.list.now = 0;
      this.kw = "";
      this.pos = { top: -100000, left: 0 };
      this.$nextTick(() => {
        this.showFlag = true;
      });
    },

    // 选中列表
    sel(text) {
      this.showFlag = false;

      if (!text) return;

      let pos = this.el.selectionEnd;
      let str1 = this.value.slice(0, pos);
      let atPos = str1.lastIndexOf("@") + 1;

      this.value2 =
        this.value.slice(0, atPos) + text + " " + this.value.slice(pos);
      this.$emit("input", this.value2);

      this.setRange(atPos + text.length + 1);
    },

    hasItem(s) {
      for (let i = 0; i < this.list.length; i++) {
        let value = this.getValue(this.list[i]);
        if (value == s) return true;
      }
      return false;
    },

    // 监听按键
    keydown(e) {
      // console.log(e);

      // 删除
      if (e.key == "Backspace") {
        // this.showFlag = false;

        let pos = this.el.selectionEnd;
        let str1 = this.value.slice(0, pos);
        let str2 = this.value.slice(pos);

        let atPos = str1.lastIndexOf("@");

        // 删除 @ 隐藏列表
        if (pos == atPos + 1) {
          this.showFlag = false;
        }

        // 列表项整体删除
        if (atPos > -1 && str1.charAt(str1.length - 1) == " ") {
          let s = str1.slice(atPos + 1, str1.length - 1);
          if (this.hasItem(s)) {
            e.preventDefault();
            e.stopPropagation();
            str1 = this.value.slice(0, atPos);
            this.value2 = str1 + str2;
            this.$emit("input", this.value2);
            this.setRange(atPos);
          }
        }
      }

      if (this.showFlag) {
        if (
          e.key === "ArrowUp" ||
          e.key === "ArrowDown" ||
          e.key === "ArrowLeft" ||
          e.key === "ArrowRight"
        ) {
          e.preventDefault();
          e.stopPropagation();
          this.$refs.list.key(e.key);
        }
        if (e.key === "Enter") {
          e.preventDefault();
          e.stopPropagation();
          this.$refs.list.sel();
        }
        if (e.key === "Escape") this.showFlag = false;
      }

      this.kw += "";
    }
  },
  watch: {
    value(val) {
      this.value2 = val;
    }
  },
  created() {
    this.value2 = this.value;
  },
  mounted() {
    this.el = this.$el.querySelector("textarea");
  }
};
</script>

<style>
.vm-panel {
  position: relative;
}
.vm-editor {
  resize: none;
  padding: 8px;
  outline: none;
  border-radius: 3px;
  border: 1px solid #aaa;
  transition: all 0.3s;
  font-family: "Avenir", Helvetica, Arial, sans-serif;
}
</style>

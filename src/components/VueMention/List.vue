<template>
  <div class="at-open" :style="{ top: pos.top + 'px', left: pos.left + 'px' }">
    <div class="at-list" :class="calc()" :style="{width:listW+'px'}">
      <div
        class="at-list-item"
        v-for="(item, i) in listC"
        @mousedown="handleMD($event,i)"
        @mouseenter="now=i"
        :key="i"
        :class="{'sel':i==now}"
      >
        <slot name="item" :item="item">{{ item }}</slot>
      </div>
      <div class="at-list-item" v-show="listC.length==0" style="text-align:center;color:#aaa;">
        <div>无匹配项</div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    // 列表数据
    list: Array,
    // 顶部打开
    top: Boolean,
    // 输入框对象
    taW: Number,
    // 位置
    pos: Object,
    // 筛选关键字
    kw: String,
    // 获取item的value
    getValue: Function,
    // value字段名
    valName: String
  },
  data() {
    return {
      now: 0,
      listW: 140
    };
  },
  computed: {
    listC() {
      // return this.list;
      return this.list.filter(
        item =>
          this.getValue(item)
            .toLowerCase()
            .indexOf(this.kw.toLowerCase()) > -1
      );
    }
  },
  methods: {
    key(type) {
      if (type == "ArrowDown") this.now = ++this.now % this.listC.length;
      if (type == "ArrowUp")
        this.now = (--this.now + this.listC.length) % this.listC.length;
      this.$nextTick(() => {
        this.$el.querySelector(".sel").scrollIntoView(false);
      });
    },
    sel() {
      this.$emit("sel", this.getValue(this.listC[this.now]));
    },
    handleMD(e, i) {
      this.now = i;
      this.sel();
      e.preventDefault();
      e.stopPropagation();
    },
    // 避免在最右侧被遮挡
    calc() {
      if (this.pos.left + this.listW > this.taW + 40)
        return "at-right" + (this.top ? " at-top" : "");
      else return "at-left" + (this.top ? " at-top" : "");
    }
  }
};
</script>

<style>
.at-open {
  position: absolute;
  z-index: 99;
}

.at-list {
  position: absolute;
  top: 1.5em;
  background: #fff;
  box-shadow: 0 1px 4px 0 rgba(0, 0, 0, 0.2);
  border-radius: 3px;
  overflow: auto;
  max-height: 200px;
}
.at-list.at-top {
  top: auto;
  bottom: 0.2em;
}
.at-list.at-left {
  left: 0;
}
.at-list.at-right {
  right: 0;
}

.at-list-item {
  line-height: 30px;
  padding: 0 10px;
  font-size: 13px;
}
.at-list-item.sel {
  cursor: pointer;
  background: rgb(2, 117, 216);
  color: #fff;
}
</style>

# Vue 输入框 @提醒插件

(演示地址)[http://demo.mxsg.fun/vue-mention/]

## 使用

1. 引入组件

   ```javascript
   import VueMention from "./path/to/VueMention/";
   ```

2. 注册组件，绑定数据

   ```javascript
   export default {
     components: { VueMention },
     data() {
       return {
         text: "Hello World! @Cyber Fei \n@mxsg.com @小明 ahaha",
         members1: ["Cyber Fei", "mxsg.com", "小明"]
       }
     },
     // ...
   }
   ```

3. 使用组件

   简单使用

   ```html
   <vue-mention v-model="text" :list="members1" />
   ```

   自定义插槽

   ```javascript
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
   ]
   ```

   ```html
   <vue-mention v-model="text" :list="members2" valName="val">
     <template slot="item" slot-scope="props">
       <b>{{ props.item.val }}</b>
     </template>
   </vue-mention>
   ```

## API

| 参数      | 说明                                 | 类型      | 默认值                                  |
| --------- | ------------------------------------ | --------- | --------------------------------------- |
| `list`    | 候选列表                             | `Array`   | `[]`                                    |
| `top`     | 列表向上展开                         | `Boolean` | `false`                                 |
| `valName` | 输入框内展示的字段（使用插槽时生效） | `String`  | `""`                                    |
| `config`  | `textarea`原生配置                   | `Object`  | `{ placeholder: "请输入...", rows: 3 }` |




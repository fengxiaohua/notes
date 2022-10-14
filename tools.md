# 一、vue-print-nb

## 1. 安装：

`yarn add vue-print-nb --save`

## 2. 引用：

main.js配置

`import Print from ‘vue-print-nb'`

`Vue.use(Print)`

## 3. 使用：

1. 在页面内
   
   ```
   import print from 'vue-print-nb'
   
   export default {
       directives: {
       print
     },
   }
   ```

2. 需要打印的区域给上 `id="printMe"`

`<a-button v-print="printObj" type="primary">打印</a-button>`

配置数据printObj数据

```
printObj: {
   id: 'printMe',
   popTitle: '需求单'
},
```

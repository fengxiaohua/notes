好文章（HTML转PDF&带分页）：

https://github.com/linwalker/render-html-to-pdf

1. 插件导入

   ```js
   import html2canvas from 'html2canvas' // 将当页面渲染成一个Canvas图片
   import Jspdf from 'jspdf'							// 转pdf文件
   import printJS from 'print-js'				// 打印
   ```

2. 打印功能实现

   ```js
   /**
        * @description: 打印按钮
        */
       print() {
         const tabEle = document.getElementById('task-tab')
         
         html2canvas(tabEle, {
           backgroundColor: null,
           useCORS: true,
           logging: false, // 日志开关，便于查看html2canvas的内部执行流程
           allowTaint: true,
           windowHeight: this.height
         }).then((canvas) => {
           const url = canvas.toDataURL()
           printJS({
             printable: url,
             type: 'image',
             style: '@page {margin:0 10mm};' // 去掉页眉页脚
           })
         })
       },
   ```

   

3. 下载为pdf

   ```
   /**
        * @description: 下载按钮
        */
       downLoad() {
         const that = this
         const ele = document.getElementById('task-container')
         const tabEle = document.getElementById('task-tab')
         const height = tabEle.style.height
         tabEle.style.height = 'auto'
         html2canvas(ele, {
           logging: false, // 日志开关，便于查看html2canvas的内部执行流程
           allowTaint: true,
           useCORS: true // 跨域设置
         }).then(function(canvas) {
           that.canvasToImg(canvas)
         }).finally(() => {
           tabEle.style.height = height
         })
       },
       canvasToImg(canvas) {
         const contentWidth = canvas.width
         const contentHeight = canvas.height
   
         // 一页pdf显示html页面生成的canvas高度;
         const pageHeight = contentWidth / 592.28 * 841.89
         // 未生成pdf的html页面高度
         let leftHeight = contentHeight
         // 页面偏移
         let position = 0
         // a4纸的尺寸[595.28,841.89]，html页面生成的canvas在pdf中图片的宽高
         const imgWidth = 595.28
         const imgHeight = 592.28 / contentWidth * contentHeight
   
         const pageData = canvas.toDataURL('image/jpeg', 1.0) // 这里设置1.0是完整页面
   
         const pdf = new Jspdf('', 'pt', 'a4')
   
         // 有两个高度需要区分，一个是html页面的实际高度，和生成pdf的页面高度(841.89)
         // 当内容未超过pdf一页显示的范围，无需分页
         if (leftHeight < pageHeight) {
           pdf.addImage(pageData, 'JPEG', 0, 0, imgWidth, imgHeight)
         } else {
           while (leftHeight > 0) {
             pdf.addImage(pageData, 'JPEG', 0, position, imgWidth, imgHeight)
             leftHeight -= pageHeight
             position -= 841.89
             // 避免添加空白页
             if (leftHeight > 0) {
               pdf.addPage()
             }
           }
         }
   
         pdf.save(`${this.taskName}.pdf`)
       }
   ```

### 问题

1. 跨域（线上测试||本地起一个服务）

   ![image-20220124155815375](/Users/xiaohang/Library/Application Support/typora-user-images/image-20220124155815375.png)

   其实到线上就好了，因为本地没有这个服务，请求不到显示跨域

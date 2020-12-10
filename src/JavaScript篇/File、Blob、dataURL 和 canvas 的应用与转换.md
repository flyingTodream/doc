## File、Blob、dataURL 和 canvas 的应用与转换

## **一、 概念介绍** 

### **1. File**

(1) 通常情况下， File 对象是来自用户在一个 input 元素上选择文件后返回的 FileList 对象,也可以是来自由拖放操作生成的 DataTransfer 对象，或者来自 htmlCanvasElement 上的 mozGetAsFile() API。

(2) File 对象是特殊类型的 Blob，且可以用在任意的 Blob 类型的 context 中。比如：FileReader, URL.createObjectURL(), createImageBitmap(), 及 XMLHttpRequest.send() 都能处理 Blob 和 File。

### **2. Blob**

(1) Blob 对象表示一个不可变、原始数据的类文件对象。它的数据可以按文本或二进制的格式进行读取，也可以转换成 ReadableStream 来用于数据操作。

(2) Blob 表示的不一定是JavaScript原生格式的数据。File 接口基于Blob，继承了 blob 的功能并将其扩展使其支持用户系统上的文件。

### **3. dataURL**

(1) Data URLs，即前缀为 data: 协议的URL，其允许内容创建者向文档中嵌入小文件。

(2) Data URLs 由四个部分组成：前缀(data:)、指示数据类型的MIME类型、如果非文本则为可选的base64标记、数据本身：data:[][;base64],

### 4. canvas

(1) Canvas API 提供了一个通过JavaScript 和 html的 canvas 元素来绘制图形的方式。它可以用于动画、游戏画面、数据可视化、图片编辑以及实时视频处理等方面。

## **二、相互转化**

### **1. File、Blob 转化成 dataURL**

*FileReader 对象允许 Web 应用程序异步读取文件(或原始数据缓冲区)内容，使用 File 或 Blob 对象指定要读取的文件或数据。*

```javascript
function fileToDataURL(file) {    
    let reader = new FileReader()    
    reader.readAsDataURL(file)    // reader 读取文件成功的回调    
    reader.onload = function(e) {      
        return reader.result    
    }
}
```



### **2. dataURL(base64) 转化成 Blob(二进制)对象**

```javascript
function dataURLToBlob(fileDataURL) {
    let arr = fileDataURL.split(','),
        mime = arr[0].match(/:(.*?);/)[1],
        bstr = atob(arr[1]),
        n = bstr.length,
        u8arr = new Uint8Array(n);
    while(n --) {
      u8arr[n] = bstr.charCodeAt(n)
    }
    return new Blob([u8arr], {type: mime})
}
```



### **3. File, Blob 文件数据绘制到 canvas**

```javascript
// 思路：File, Blob ——> dataURL ——> canvas

function fileAndBlobToCanvas(fileDataURL) {
    let img = new Image()
    img.src = fileDataURL
    let canvas = document.createElement('canvas')
    if(!canvas.getContext) {
      alert('浏览器不支持canvas')
      return;
    }
    let ctx = canvas.getContext('2d')
    document.getElementById('container').appendChild(canvas)
    img.onload = function() {
      ctx.drawImage(img, 0, 0, canvas.width, canvas.height)
    }
}
```



### **4. 从 canvas 中获取文件 dataURL**

```javascript
function canvasToDataURL() {
    let canvas = document.createElement('canvas')
    let canvasDataURL = canvas.toDataURL('image/png', 1.0)
    return canvasDataURL
}
```
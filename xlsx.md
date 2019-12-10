# 前端实现excel（csv）文件导入导出

js-xlsx是一款非常方便的只需要纯JS即可读取和导出excel的工具库，功能强大，支持格式众多，支持xls、xlsx、ods(一种OpenOffice专有表格文件格式)等十几种格式。本文全部都是以xlsx格式为例。[官方github](https://github.com/SheetJS/js-xlsx)，[参考文档](https://docs.sheetjs.com/#sheetjs-js-xlsx)


## 一、兼容性

几乎兼容所有浏览器，满足不同需求，如图所示：<br>

![](https://raw.githubusercontent.com/zxcweb/pic-go/master/img/20191207175544.png)

## 二、转化图解
转化的基本流程：

![](https://raw.githubusercontent.com/zxcweb/pic-go/master/img/20191207180647.png)

## 三、安装及使用
安装
```bash
npm install xlsx --save-dev
```
or,一般情况下用xlsx.core.min.js就够了，xlsx.full.min.js则是包含了所有功能模块
```html
<script type="text/javascript" src="./js/xlsx.core.min.js"></script>
```

使用
```js
import XLSX from 'xlsx'
```
## 四、excel解读

我们创建一个excel会经历一下过程：
1. 创建一个工作薄
2. 创建一个sheet
3. 创建表格行列等

所以我们用js-xlsx创建excel同样的道理
1. 创建工作薄（WorkBook）
2. 创建sheet（WorkBook.Sheet）
3. 创建表格行列（WorkBook.sheet[]）

## 五、读取excel

读取excel主要是通过 ```XLSX.read(data, {type: type})``` 方法来实现，返回一个叫WorkBook的对象，type主要取值如下：
> base64: 以base64方式读取；<br>
> binary: BinaryString格式二进制<br>
> string: UTF8编码的字符串；<br>
> buffer: nodejs Buffer；<br>
> array: Uint8Array，8位无符号数组；<br>
> file: 文件的路径（仅nodejs下支持）；<br>

1. 读取本地excel文件
2. 读取excel文件转化wookbook对象
```js
// 读取本地excel文件
function readWorkbookFromLocalFile(file, callback) {
	var reader = new FileReader();
	reader.onload = function(e) {
        var data = e.target.result;
        // 读取二进制的excel
		var workbook = XLSX.read(data, {type: 'binary'});
		if(callback) callback(workbook);
	};
	reader.readAsBinaryString(file);
}
// 读取远程文件
function readWorkbookFromRemoteFile(url, callback) {
    var xhr = new XMLHttpRequest();
    xhr.open('get', url, true);
    xhr.responseType = 'arraybuffer';
    xhr.onload = function (e) {
        if (xhr.status == 200) {
            var data = new Uint8Array(xhr.response)
            var workbook = XLSX.read(data, { type: 'array' });
            if (callback) callback(workbook);
        }
    };
    xhr.send();
}
```

## 六、WorkBook对象

来看读取文件后的wb对象<br>
可以看到，SheetNames里面保存了所有的sheet名字，然后Sheets则保存了每个sheet的具体内容（我们称之为Sheet Object）。每一个sheet是通过类似A1这样的键值保存每个单元格的内容，我们称之为单元格对象（Cell Object）
> t：表示内容类型，s表示string类型，n表示number类型，b表示boolean类型，d表示date类型，等等<br>
> v：表示原始值；<br>
> f：表示公式，如B2+B3；<br>
> h：HTML内容<br>
> w：格式化后的内容<br>
> r：富文本内容rich text<br>
> 等等
![](https://raw.githubusercontent.com/zxcweb/pic-go/master/img/20191208105856.png)

## 七、Sheet Object

> sheet['!ref']：表示所有单元格的范围，例如从A1到F8则记录为A1:F8;<br>
> sheet[!merges]：存放一些单元格合并信息，是一个数组，每个数组由包含s和e构成的对象组成，s表示开始，e表示结束，r表示行，c表示列;<br>
> 等等

![](https://raw.githubusercontent.com/zxcweb/pic-go/master/img/20191210091858.png)

结果如下：

![](https://raw.githubusercontent.com/zxcweb/pic-go/master/img/20191210091918.png)

## 八、XLSX.utils常用导出类型

XLSX对象
![](https://raw.githubusercontent.com/zxcweb/pic-go/master/img/20191208111201.png)
常用的主要是：<br>
- XLSX.utils.sheet_to_csv：生成CSV格式
- XLSX.utils.sheet_to_txt：生成纯文本格式
- XLSX.utils.sheet_to_html：生成HTML格式
- XLSX.utils.sheet_to_json：输出JSON格式

![](https://raw.githubusercontent.com/zxcweb/pic-go/master/img/20191208111301.png)

## 九、excel导出

首先介绍三个方法：
> XLSX.utils.aoa_to_sheet: 这个工具类最强大也最实用了，将一个二维数组转成sheet，会自动处理number、string、boolean、date等类型数据；<br>
> XLSX.utils.table_to_sheet: 将一个table dom直接转成sheet，会自动识别colspan和rowspan并将其转成对应的单元格合并；<br>
> XLSX.utils.json_to_sheet: 将一个由对象组成的数组转成sheet；<br>

以json_to_sheet为例这里介绍一下如何导出excel
1. 创建一个工作薄
```js
let wb = XLSX.utils.book_new()
```
2. 创建sheet对象
```js
// 一个简单的sheet
let sheetData = [
    { department: '行政部', count: 2 },
    { department: '技术部', count: 2 }
]
let sheet = XLSX.utils.json_to_sheet(sheetData)
```
3. 如果有合并单元格
```js
sheet['!merges'] = [
    {
        e: { c: 0, r: 1 }, // 合并结束位置 c:列位置 r:表示行位置
        s: { c: 0, r: 0 } // 合并开始位置
    }
]
```
4. 如果多个sheetData
```js
XLSX.utils.book_append_sheet(wb, sheet2, sheetName1)
XLSX.utils.book_append_sheet(wb, sheet3, sheetName2)
...
...
```

5. 转化格式，导出文件
```js
// 创建工作薄blob
const workbookBlob = workbook2blob(wb)
// 导出工作薄
openDownloadDialog(workbookBlob, fn)
```









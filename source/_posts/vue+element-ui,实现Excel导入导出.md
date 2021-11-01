---
title: vue+element-ui,实现Excel导入导出
date: 2021-11-01
tags: [前端, excel]
---

## 一、准备工作

1、在 vue 中使用导入导出，需要下载 3 个依赖包和 2 个 js 包：

**使用 npm 安装：**

```js
npm install -S file-saver xlsx
npm install -D script-loader
npm install xlsx
```

<!-- more -->

2、在 main.js 中引入 XLSX

```js
import XLSX from "xlsx";
Vue.use(XLSX);
```

3、在 src 目录下新建一个 excel 文件夹引入 Blob.js 和 expor2Excal.js

**Blob.js**

```js
/* eslint-disable */
/* Blob.js
 * A Blob implementation.
 * 2014-05-27
 *
 * By Eli Grey, http://eligrey.com
 * By Devin Samarin, https://github.com/eboyjr
 * License: X11/MIT
 *   See LICENSE.md
 */

/*global self, unescape */
/*jslint bitwise: true, regexp: true, confusion: true, es5: true, vars: true, white: true,
 plusplus: true */

/*! @source http://purl.eligrey.com/github/Blob.js/blob/master/Blob.js */

(function (view) {
  "use strict";

  view.URL = view.URL || view.webkitURL;

  if (view.Blob && view.URL) {
    try {
      new Blob();
      return;
    } catch (e) {}
  }

  // Internally we use a BlobBuilder implementation to base Blob off of
  // in order to support older browsers that only have BlobBuilder
  var BlobBuilder =
    view.BlobBuilder ||
    view.WebKitBlobBuilder ||
    view.MozBlobBuilder ||
    (function (view) {
      var get_class = function (object) {
          return Object.prototype.toString
            .call(object)
            .match(/^\[object\s(.*)\]$/)[1];
        },
        FakeBlobBuilder = function BlobBuilder() {
          this.data = [];
        },
        FakeBlob = function Blob(data, type, encoding) {
          this.data = data;
          this.size = data.length;
          this.type = type;
          this.encoding = encoding;
        },
        FBB_proto = FakeBlobBuilder.prototype,
        FB_proto = FakeBlob.prototype,
        FileReaderSync = view.FileReaderSync,
        FileException = function (type) {
          this.code = this[(this.name = type)];
        },
        file_ex_codes = (
          "NOT_FOUND_ERR SECURITY_ERR ABORT_ERR NOT_READABLE_ERR ENCODING_ERR " +
          "NO_MODIFICATION_ALLOWED_ERR INVALID_STATE_ERR SYNTAX_ERR"
        ).split(" "),
        file_ex_code = file_ex_codes.length,
        real_URL = view.URL || view.webkitURL || view,
        real_create_object_URL = real_URL.createObjectURL,
        real_revoke_object_URL = real_URL.revokeObjectURL,
        URL = real_URL,
        btoa = view.btoa,
        atob = view.atob,
        ArrayBuffer = view.ArrayBuffer,
        Uint8Array = view.Uint8Array;
      FakeBlob.fake = FB_proto.fake = true;
      while (file_ex_code--) {
        FileException.prototype[file_ex_codes[file_ex_code]] = file_ex_code + 1;
      }
      if (!real_URL.createObjectURL) {
        URL = view.URL = {};
      }
      URL.createObjectURL = function (blob) {
        var type = blob.type,
          data_URI_header;
        if (type === null) {
          type = "application/octet-stream";
        }
        if (blob instanceof FakeBlob) {
          data_URI_header = "data:" + type;
          if (blob.encoding === "base64") {
            return data_URI_header + ";base64," + blob.data;
          } else if (blob.encoding === "URI") {
            return data_URI_header + "," + decodeURIComponent(blob.data);
          }
          if (btoa) {
            return data_URI_header + ";base64," + btoa(blob.data);
          } else {
            return data_URI_header + "," + encodeURIComponent(blob.data);
          }
        } else if (real_create_object_URL) {
          return real_create_object_URL.call(real_URL, blob);
        }
      };
      URL.revokeObjectURL = function (object_URL) {
        if (object_URL.substring(0, 5) !== "data:" && real_revoke_object_URL) {
          real_revoke_object_URL.call(real_URL, object_URL);
        }
      };
      FBB_proto.append = function (data /*, endings*/) {
        var bb = this.data;
        // decode data to a binary string
        if (
          Uint8Array &&
          (data instanceof ArrayBuffer || data instanceof Uint8Array)
        ) {
          var str = "",
            buf = new Uint8Array(data),
            i = 0,
            buf_len = buf.length;
          for (; i < buf_len; i++) {
            str += String.fromCharCode(buf[i]);
          }
          bb.push(str);
        } else if (get_class(data) === "Blob" || get_class(data) === "File") {
          if (FileReaderSync) {
            var fr = new FileReaderSync();
            bb.push(fr.readAsBinaryString(data));
          } else {
            // async FileReader won't work as BlobBuilder is sync
            throw new FileException("NOT_READABLE_ERR");
          }
        } else if (data instanceof FakeBlob) {
          if (data.encoding === "base64" && atob) {
            bb.push(atob(data.data));
          } else if (data.encoding === "URI") {
            bb.push(decodeURIComponent(data.data));
          } else if (data.encoding === "raw") {
            bb.push(data.data);
          }
        } else {
          if (typeof data !== "string") {
            data += ""; // convert unsupported types to strings
          }
          // decode UTF-16 to binary string
          bb.push(unescape(encodeURIComponent(data)));
        }
      };
      FBB_proto.getBlob = function (type) {
        if (!arguments.length) {
          type = null;
        }
        return new FakeBlob(this.data.join(""), type, "raw");
      };
      FBB_proto.toString = function () {
        return "[object BlobBuilder]";
      };
      FB_proto.slice = function (start, end, type) {
        var args = arguments.length;
        if (args < 3) {
          type = null;
        }
        return new FakeBlob(
          this.data.slice(start, args > 1 ? end : this.data.length),
          type,
          this.encoding
        );
      };
      FB_proto.toString = function () {
        return "[object Blob]";
      };
      FB_proto.close = function () {
        this.size = this.data.length = 0;
      };
      return FakeBlobBuilder;
    })(view);

  view.Blob = function Blob(blobParts, options) {
    var type = options ? options.type || "" : "";
    var builder = new BlobBuilder();
    if (blobParts) {
      for (var i = 0, len = blobParts.length; i < len; i++) {
        builder.append(blobParts[i]);
      }
    }
    return builder.getBlob(type);
  };
})(
  (typeof self !== "undefined" && self) ||
    (typeof window !== "undefined" && window) ||
    this.content ||
    this
);
```

**Export2Excel.js**

```js
/* eslint-disable */
require("script-loader!file-saver");
require("./Blob.js");
require("script-loader!xlsx/dist/xlsx.core.min");
function generateArray(table) {
  var out = [];
  var rows = table.querySelectorAll("tr");
  var ranges = [];
  for (var R = 0; R < rows.length; ++R) {
    var outRow = [];
    var row = rows[R];
    var columns = row.querySelectorAll("td");
    for (var C = 0; C < columns.length; ++C) {
      var cell = columns[C];
      var colspan = cell.getAttribute("colspan");
      var rowspan = cell.getAttribute("rowspan");
      var cellValue = cell.innerText;
      if (cellValue !== "" && cellValue == +cellValue) cellValue = +cellValue;

      //Skip ranges
      ranges.forEach(function (range) {
        if (
          R >= range.s.r &&
          R <= range.e.r &&
          outRow.length >= range.s.c &&
          outRow.length <= range.e.c
        ) {
          for (var i = 0; i <= range.e.c - range.s.c; ++i) outRow.push(null);
        }
      });

      //Handle Row Span
      if (rowspan || colspan) {
        rowspan = rowspan || 1;
        colspan = colspan || 1;
        ranges.push({
          s: { r: R, c: outRow.length },
          e: { r: R + rowspan - 1, c: outRow.length + colspan - 1 },
        });
      }
      //Handle Value
      outRow.push(cellValue !== "" ? cellValue : null);

      //Handle Colspan
      if (colspan) for (var k = 0; k < colspan - 1; ++k) outRow.push(null);
    }
    out.push(outRow);
  }
  return [out, ranges];
}

function datenum(v, date1904) {
  if (date1904) v += 1462;
  var epoch = Date.parse(v);
  return (epoch - new Date(Date.UTC(1899, 11, 30))) / (24 * 60 * 60 * 1000);
}

function sheet_from_array_of_arrays(data, opts) {
  var ws = {};
  var range = { s: { c: 10000000, r: 10000000 }, e: { c: 0, r: 0 } };
  for (var R = 0; R != data.length; ++R) {
    for (var C = 0; C != data[R].length; ++C) {
      if (range.s.r > R) range.s.r = R;
      if (range.s.c > C) range.s.c = C;
      if (range.e.r < R) range.e.r = R;
      if (range.e.c < C) range.e.c = C;
      var cell = { v: data[R][C] };
      if (cell.v == null) continue;
      var cell_ref = XLSX.utils.encode_cell({ c: C, r: R });

      if (typeof cell.v === "number") cell.t = "n";
      else if (typeof cell.v === "boolean") cell.t = "b";
      else if (cell.v instanceof Date) {
        cell.t = "n";
        cell.z = XLSX.SSF._table[14];
        cell.v = datenum(cell.v);
      } else cell.t = "s";

      ws[cell_ref] = cell;
    }
  }
  if (range.s.c < 10000000) ws["!ref"] = XLSX.utils.encode_range(range);
  return ws;
}

function Workbook() {
  if (!(this instanceof Workbook)) return new Workbook();
  this.SheetNames = [];
  this.Sheets = {};
}

function s2ab(s) {
  var buf = new ArrayBuffer(s.length);
  var view = new Uint8Array(buf);
  for (var i = 0; i != s.length; ++i) view[i] = s.charCodeAt(i) & 0xff;
  return buf;
}

export function export_table_to_excel(id) {
  var theTable = document.getElementById(id);
  console.log("a");
  var oo = generateArray(theTable);
  var ranges = oo[1];

  /* original data */
  var data = oo[0];
  var ws_name = "SheetJS";
  console.log(data);

  var wb = new Workbook(),
    ws = sheet_from_array_of_arrays(data);

  /* add ranges to worksheet */
  // ws['!cols'] = ['apple', 'banan'];
  ws["!merges"] = ranges;

  /* add worksheet to workbook */
  wb.SheetNames.push(ws_name);
  wb.Sheets[ws_name] = ws;

  var wbout = XLSX.write(wb, {
    bookType: "xlsx",
    bookSST: false,
    type: "binary",
  });

  saveAs(
    new Blob([s2ab(wbout)], { type: "application/octet-stream" }),
    "test.xlsx"
  );
}

function formatJson(jsonData) {
  console.log(jsonData);
}
export function export_json_to_excel(th, jsonData, defaultTitle) {
  /* original data */

  var data = jsonData;
  data.unshift(th);
  var ws_name = "SheetJS";

  var wb = new Workbook(),
    ws = sheet_from_array_of_arrays(data);

  /* add worksheet to workbook */
  wb.SheetNames.push(ws_name);
  wb.Sheets[ws_name] = ws;

  var wbout = XLSX.write(wb, {
    bookType: "xlsx",
    bookSST: false,
    type: "binary",
  });
  var title = defaultTitle || "列表";
  saveAs(
    new Blob([s2ab(wbout)], { type: "application/octet-stream" }),
    title + ".xlsx"
  );
}
```

## 一、导出

```js
export2Excel() {
　require.ensure([], () => {
　  const { export_json_to_excel } = require('../../vendor/Export2Excel');// 这里 require 写你的Export2Excel.js的绝对地址
　　const tHeader = ['序号', 'IMSI', 'MSISDN', '证件号码', '姓名']; //对应表格输出的title
　　const filterVal = ['ID', 'imsi', 'msisdn', 'address', 'name']; // 对应表格输出的数据
　　const list = this.tableData;
　　const data = this.formatJson(filterVal, list);
　　export_json_to_excel(tHeader, data, '列表excel'); //对应下载文件的名字
　})
},
formatJson(filterVal, jsonData) {
  return jsonData.map(v => filterVal.map(j => v[j]))
}
```

## 二、导入

1、使用 el-upload 组件辅助上传 excel 文件

```html
<!--limit:最大上传数，on-exceed：超过最大上传数量时的操作  -->
<el-upload
  class="upload-demo"
  action=""
  :on-change="handleChange"
  :on-remove="handleRemove"
  :on-exceed="handleExceed"
  :limit="limitUpload"
  accept="application/vnd.openxmlformats-    
        officedocument.spreadsheetml.sheet,application/vnd.ms-excel"
  :auto-upload="false"
>
  <el-button size="small" type="primary">点击上传</el-button>
</el-upload>
```

2、定义所需要的方法，代码如下（为了美观，加入了一些提示语和提示框）

```js
//上传文件时处理方法
handleChange(file, fileList){
    this.fileTemp = file.raw;
    if(this.fileTemp){
        if((this.fileTemp.type == 'application/vnd.openxmlformats-officedocument.spreadsheetml.sheet')
            || (this.fileTemp.type == 'application/vnd.ms-excel')){
            this.importfxx(this.fileTemp);
        } else {
            this.$message({
                type:'warning',
                message:'附件格式错误，请删除后重新上传！'
            })
        }
    } else {
        this.$message({
            type:'warning',
            message:'请上传附件！'
        })
    }
},
//超出最大上传文件数量时的处理方法
handleExceed(){
    this.$message({
        type:'warning',
        message:'超出最大上传文件数量的限制！'
    })
    return;
},
//移除文件的操作方法
handleRemove(file,fileList){
    this.fileTemp = null
},
```

3、最重要的读取 excel 文件的方法：

```js
importfxx(obj) {
  let _this = this;
  let inputDOM = this.$refs.inputer;
  // 通过DOM取文件数据

  this.file = event.currentTarget.files[0];

  var rABS = false; //是否将文件读取为二进制字符串
  var f = this.file;

  var reader = new FileReader();
  //if (!FileReader.prototype.readAsBinaryString) {
  FileReader.prototype.readAsBinaryString = function(f) {
    var binary = "";
    var rABS = false; //是否将文件读取为二进制字符串
    var pt = this;
    var wb; //读取完成的数据
    var outdata;
    var reader = new FileReader();
    reader.onload = function(e) {
      var bytes = new Uint8Array(reader.result);
      var length = bytes.byteLength;
      for (var i = 0; i < length; i++) {
        binary += String.fromCharCode(bytes[i]);
      }
      //此处引入，用于解析excel
      var XLSX = require("xlsx");
      if (rABS) {
        wb = XLSX.read(btoa(fixdata(binary)), {
        //手动转化
        type: "base64"
        });
      } else {
        wb = XLSX.read(binary, {
        type: "binary"
        });
      }
      outdata = XLSX.utils.sheet_to_json(wb.Sheets[wb.SheetNames[0]]);
      //outdata就是读取的数据（不包含标题行即表头，表头会作为对象的下标）
      //此处可对数据进行处理
      // let arr = [];
      // outdata.map(v => {
      //     let obj = {}
      //     obj.code = v['Code']
      //     obj.name = v['Name']
      //     obj.pro = v['province']
      //     obj.cit = v['city']
      //     obj.dis = v['district']
      //     arr.push(obj)
      // });
      // _this.da=arr;
      // _this.dalen=arr.length;
      // return arr;
    };
    reader.readAsArrayBuffer(f);
  };
  if (rABS) {
    reader.readAsArrayBuffer(f);
  } else {
    reader.readAsBinaryString(f);
  }
}
```

ps：做 excel 表格导入时，处理导入数据

```js
//拿到的数据：
let dataArray = [{ 卡号: "123", 手机号: "18374174400" }];
//想要的数据：
let newObjArray = [{ card: "123", mobile: "18374174400" }];
```

数据处理方法:

```js
//处理导入的数据
let dataArray=[{'卡号':'123','手机号':'18374174400'}];
initDate(dataArray) {
  let newObjArray = [];
  let keyMap = { '卡号': 'card', '手机号': 'mobile' }
  dataArray.forEach(item => {
    var objs = Object.keys(item).reduce((newData, key) => {
      let newKey = keyMap[key] || key;
      newData[newKey] = item[key];
      return newData;
    }, {})
    newObjArray.push(objs);
  })
  console.log("newObjArray", newObjArray)
}
```

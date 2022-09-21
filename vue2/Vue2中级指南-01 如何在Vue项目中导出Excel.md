# Excel 导出
Excel 的导入导出都是依赖于js-xlsx来实现的。

在 js-xlsx的基础上又封装了Export2Excel.js来方便导出数据。
## 使用
由于 Export2Excel不仅依赖js-xlsx还依赖file-saver和script-loader。

所以你先需要安装如下命令：

```
npm install xlsx file-saver -S
npm install script-loader -S -D
```
由于js-xlsx体积还是很大的，导出功能也不是一个非常常用的功能，所以使用的时候建议使用懒加载。使用方法如下：

```
import('@/vendor/Export2Excel').then(excel => {
  excel.export_json_to_excel({
    header: tHeader, //表头 必填
    data, //具体数据 必填
    filename: 'excel-list', //非必填, 导出文件的名字
    autoWidth: true, //非必填, 导出文件的排列方式
    bookType: 'xlsx' //非必填, 导出文件的格式
  })
})
```
### 注意
在v3.9.1+以后的版本中移除了对 Bolb 的兼容性代码，如果你还需要兼容很低版本的浏览器可以手动引入blob-polyfill进行兼容。
## 参数

```
参数	    说明	          类型	    可选值	           默认值
header	    导出数据的表头	  Array	    /	                   []
data	    导出的具体数据	  Array	    /	                   []
filename    导出文件名             String    /                      excel-list
autoWidth   单元格是否要自适应宽度  Boolean   true / false           true
bookType    导出文件类型           String    xlsx, csv, txt, more   xlsx
```
### 项目实战
使用脚手架搭建出基本项目雏形,这时候在src目录下新建一个vendor(文件名自己定义)文件夹,新建一个Export2Excel.js文件,这个文件里面在js-xlsx的基础上又封装了Export2Excel.js来方便导出数据。
* 目录如下
![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/4/18/16a2e7b05ada4bb5~tplv-t2oaga2asx-image.image)
* Export2Excel.js代码如下

```
require('script-loader!file-saver');
import XLSX from 'xlsx'

function generateArray(table) {
  var out = [];
  var rows = table.querySelectorAll('tr');
  var ranges = [];
  for (var R = 0; R < rows.length; ++R) {
    var outRow = [];
    var row = rows[R];
    var columns = row.querySelectorAll('td');
    for (var C = 0; C < columns.length; ++C) {
      var cell = columns[C];
      var colspan = cell.getAttribute('colspan');
      var rowspan = cell.getAttribute('rowspan');
      var cellValue = cell.innerText;
      if (cellValue !== "" && cellValue == +cellValue) cellValue = +cellValue;

      //Skip ranges
      ranges.forEach(function (range) {
        if (R >= range.s.r && R <= range.e.r && outRow.length >= range.s.c && outRow.length <= range.e.c) {
          for (var i = 0; i <= range.e.c - range.s.c; ++i) outRow.push(null);
        }
      });

      //Handle Row Span
      if (rowspan || colspan) {
        rowspan = rowspan || 1;
        colspan = colspan || 1;
        ranges.push({
          s: {
            r: R,
            c: outRow.length
          },
          e: {
            r: R + rowspan - 1,
            c: outRow.length + colspan - 1
          }
        });
      };

      //Handle Value
      outRow.push(cellValue !== "" ? cellValue : null);

      //Handle Colspan
      if (colspan)
        for (var k = 0; k < colspan - 1; ++k) outRow.push(null);
    }
    out.push(outRow);
  }
  return [out, ranges];
};

function datenum(v, date1904) {
  if (date1904) v += 1462;
  var epoch = Date.parse(v);
  return (epoch - new Date(Date.UTC(1899, 11, 30))) / (24 * 60 * 60 * 1000);
}

function sheet_from_array_of_arrays(data, opts) {
  var ws = {};
  var range = {
    s: {
      c: 10000000,
      r: 10000000
    },
    e: {
      c: 0,
      r: 0
    }
  };
  for (var R = 0; R != data.length; ++R) {
    for (var C = 0; C != data[R].length; ++C) {
      if (range.s.r > R) range.s.r = R;
      if (range.s.c > C) range.s.c = C;
      if (range.e.r < R) range.e.r = R;
      if (range.e.c < C) range.e.c = C;
      var cell = {
        v: data[R][C]
      };
      if (cell.v == null) continue;
      var cell_ref = XLSX.utils.encode_cell({
        c: C,
        r: R
      });

      if (typeof cell.v === 'number') cell.t = 'n';
      else if (typeof cell.v === 'boolean') cell.t = 'b';
      else if (cell.v instanceof Date) {
        cell.t = 'n';
        cell.z = XLSX.SSF._table[14];
        cell.v = datenum(cell.v);
      } else cell.t = 's';

      ws[cell_ref] = cell;
    }
  }
  if (range.s.c < 10000000) ws['!ref'] = XLSX.utils.encode_range(range);
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
  for (var i = 0; i != s.length; ++i) view[i] = s.charCodeAt(i) & 0xFF;
  return buf;
}

export function export_table_to_excel(id) {
  var theTable = document.getElementById(id);
  var oo = generateArray(theTable);
  var ranges = oo[1];

  /* original data */
  var data = oo[0];
  var ws_name = "SheetJS";

  var wb = new Workbook(),
    ws = sheet_from_array_of_arrays(data);

  /* add ranges to worksheet */
  // ws['!cols'] = ['apple', 'banan'];
  ws['!merges'] = ranges;

  /* add worksheet to workbook */
  wb.SheetNames.push(ws_name);
  wb.Sheets[ws_name] = ws;

  var wbout = XLSX.write(wb, {
    bookType: 'xlsx',
    bookSST: false,
    type: 'binary'
  });

  saveAs(new Blob([s2ab(wbout)], {
    type: "application/octet-stream"
  }), "test.xlsx")
}

export function export_json_to_excel({
  multiHeader = [],
  header,
  data,
  filename,
  merges = [],
  autoWidth = true,
  bookType = 'xlsx'
} = {}) {
  /* original data */
  filename = filename || 'excel-list'
  data = [...data]
  data.unshift(header);

  for (let i = multiHeader.length - 1; i > -1; i--) {
    data.unshift(multiHeader[i])
  }

  var ws_name = "SheetJS";
  var wb = new Workbook(),
    ws = sheet_from_array_of_arrays(data);

  if (merges.length > 0) {
    if (!ws['!merges']) ws['!merges'] = [];
    merges.forEach(item => {
      ws['!merges'].push(XLSX.utils.decode_range(item))
    })
  }

  if (autoWidth) {
    /*设置worksheet每列的最大宽度*/
    const colWidth = data.map(row => row.map(val => {
      /*先判断是否为null/undefined*/
      if (val == null) {
        return {
          'wch': 10
        };
      }
      /*再判断是否为中文*/
      else if (val.toString().charCodeAt(0) > 255) {
        return {
          'wch': val.toString().length * 2
        };
      } else {
        return {
          'wch': val.toString().length
        };
      }
    }))
    /*以第一行为初始值*/
    let result = colWidth[0];
    for (let i = 1; i < colWidth.length; i++) {
      for (let j = 0; j < colWidth[i].length; j++) {
        if (result[j]['wch'] < colWidth[i][j]['wch']) {
          result[j]['wch'] = colWidth[i][j]['wch'];
        }
      }
    }
    ws['!cols'] = result;
  }

  /* add worksheet to workbook */
  wb.SheetNames.push(ws_name);
  wb.Sheets[ws_name] = ws;

  var wbout = XLSX.write(wb, {
    bookType: bookType,
    bookSST: false,
    type: 'binary'
  });
  saveAs(new Blob([s2ab(wbout)], {
    type: "application/octet-stream"
  }), `${filename}.${bookType}`);
}
```
* 新建一个exportExcel.vue模板用于导出Excel表格,使用代码如下

```
<template>
  <div class="exportExcel">
    <div class="excel-header">
      <!--导出文件名称-->
      <div class="filename">
        <label class="radio-label" style="padding-left:0;">Filename:</label>
        <el-input
          v-model="filename"
          placeholder="请输入导出文件名"
          style="width:340px;"
        prefix-icon="el-icon-document" />
      </div>
      <!--设置表格导出的宽度是否自动-->
      <div class="autoWidth">
        <label class="radio-label">Cell Auto-Width:</label>
        <el-radio-group v-model="autoWidth">
          <el-radio :label="true" border>True</el-radio>
          <el-radio :label="false" border>False</el-radio>
        </el-radio-group>
      </div>
      <!--导出文件后缀类型-->
      <div class="bookType">
        <label class="radio-label">Book Type:</label>
        <el-select v-model="bookType" style="width:120px;">
          <el-option v-for="item in options" :key="item" :label="item" :value="item"/>
        </el-select>
      </div>
      <!--导出文件-->
      <div class="download">
        <el-button
          :loading="downloadLoading"
          type="primary"
          icon="document"
        @click="handleDownload">export Excel</el-button>
      </div>
    </div>

    <el-table
      v-loading="listLoading"
      :data="list"
      element-loading-text="拼命加载中"
      border
      fit
      highlight-current-row
      height="390px"
    >
      <el-table-column align="center" label="序号" width="95">
        <template slot-scope="scope">{{ scope.$index }}</template>
      </el-table-column>
      <el-table-column label="订单号" width="230">
        <template slot-scope="scope">{{ scope.row.title }}</template>
      </el-table-column>
      <el-table-column label="菜品" align="center">
        <template slot-scope="scope">{{ scope.row.foods }}</template>
      </el-table-column>
      <el-table-column label="收银员" width="110" align="center">
        <template slot-scope="scope">
          <el-tag>{{ scope.row.author }}</el-tag>
        </template>
      </el-table-column>
      <el-table-column label="金额" width="115" align="center">
        <template slot-scope="scope">{{ scope.row.pageviews }}</template>
      </el-table-column>
      <el-table-column align="center" label="时间" width="220">
        <template slot-scope="scope">
          <i class="el-icon-time"/>
          <span>{{ scope.row.timestamp | parseTime('{y}-{m}-{d} {h}:{i}') }}</span>
        </template>
      </el-table-column>
    </el-table>
  </div>
</template>

export default {
  name: "exportExcelDialog",
  data() {
    return {
      // 列表内容
      list: null,
      // loding窗口状态
      listLoading: true,
      // 下载loding窗口状态
      downloadLoading: false,
      // 导出文件名称
      filename: "",
      // 导出表格宽度是否auto
      autoWidth: true,
      // 导出文件格式
      bookType: "xlsx",
      // 默认导出文件后缀类型
      options: ["xlsx", "csv", "txt"]
    };
  },
  methods: {
    // 导出Excel表格
    handleDownload() {
      this.downloadLoading = true;
      // 懒加载该用法
      import("@/vendor/Export2Excel").then(excel => {
        // 设置导出表格的头部
        const tHeader = ["序号", "订单号", "菜品", "收银员", "金额", "时间"];
        // 设置要导出的属性
        const filterVal = [
          "id",
          "title",
          "foods",
          "author",
          "pageviews",
          "display_time"
        ];
        // 获取当前展示的表格数据
        const list = this.list;
        // 将要导出的数据进行一个过滤
        const data = this.formatJson(filterVal, list);
        // 调用我们封装好的方法进行导出Excel
        excel.export_json_to_excel({
          // 导出的头部
          header: tHeader,
          // 导出的内容
          data,
          // 导出的文件名称
          filename: this.filename,
          // 导出的表格宽度是否自动
          autoWidth: this.autoWidth,
          // 导出文件的后缀类型
          bookType: this.bookType
        });
        this.downloadLoading = false;
      });
    },
    // 对要导出的内容进行过滤
    formatJson(filterVal, jsonData) {
      return jsonData.map(v =>
        filterVal.map(j => {
          if (j === "timestamp") {
            return this.parseTime(v[j]);
          } else {
            return v[j];
          }
        })
      );
    },
    // 过滤时间
    parseTime(time, cFormat) {
      if (arguments.length === 0) {
        return null;
      }
      const format = cFormat || "{y}-{m}-{d} {h}:{i}:{s}";
      let date;
      if (typeof time === "object") {
        date = time;
      } else {
        if (typeof time === "string" && /^[0-9]+$/.test(time)) {
          time = parseInt(time);
        }
        if (typeof time === "number" && time.toString().length === 10) {
          time = time * 1000;
        }
        date = new Date(time);
      }
      const formatObj = {
        y: date.getFullYear(),
        m: date.getMonth() + 1,
        d: date.getDate(),
        h: date.getHours(),
        i: date.getMinutes(),
        s: date.getSeconds(),
        a: date.getDay()
      };
      const timeStr = format.replace(/{(y|m|d|h|i|s|a)+}/g, (result, key) => {
        let value = formatObj[key];
        // Note: getDay() returns 0 on Sunday
        if (key === "a") {
          return ["日", "一", "二", "三", "四", "五", "六"][value];
        }
        if (result.length > 0 && value < 10) {
          value = "0" + value;
        }
        return value || 0;
      });
      return timeStr;
    }
  },
  mounted() {
    // 模拟获取数据
    setTimeout(() => {
      this.list = [
        {
          timestamp: 1432179778664,
          author: "Charles",
          comment_disabled: true,
          content_short: "mock data",
          display_time: "1994-05-25 23:37:25",
          foods: "鸡翅、萝卜、牛肉、红烧大闸蟹、红烧鸡翅",
          id: 1,
          image_uri:
            "https://wpimg.wallstcn.com/e4558086-631c-425c-9430-56ffb46e70b3",
          importance: 3,
          pageviews: 2864,
          platforms: ["a-platform"],
          reviewer: "Sandra",
          status: "published",
          title: "O20190407135010000000001",
          type: "CN"
        },
        {
          timestamp: 1432179778664,
          author: "Charles",
          comment_disabled: true,
          content_short: "mock data",
          display_time: "1994-05-25 23:37:25",
          foods: "鸡翅、萝卜、牛肉、红烧大闸蟹、红烧鸡翅",
          id: 1,
          image_uri:
            "https://wpimg.wallstcn.com/e4558086-631c-425c-9430-56ffb46e70b3",
          importance: 3,
          pageviews: 2864,
          platforms: ["a-platform"],
          reviewer: "Sandra",
          status: "published",
          title: "O20190407135010000000001",
          type: "CN"
        },
        {
          timestamp: 1432179778664,
          author: "Charles",
          comment_disabled: true,
          content_short: "mock data",
          display_time: "1994-05-25 23:37:25",
          foods: "鸡翅、萝卜、牛肉、红烧大闸蟹、红烧鸡翅",
          id: 1,
          image_uri:
            "https://wpimg.wallstcn.com/e4558086-631c-425c-9430-56ffb46e70b3",
          importance: 3,
          pageviews: 2864,
          platforms: ["a-platform"],
          reviewer: "Sandra",
          status: "published",
          title: "O20190407135010000000001",
          type: "CN"
        },
        {
          timestamp: 1432179778664,
          author: "Charles",
          comment_disabled: true,
          content_short: "mock data",
          display_time: "1994-05-25 23:37:25",
          foods: "鸡翅、萝卜、牛肉、红烧大闸蟹、红烧鸡翅",
          id: 1,
          image_uri:
            "https://wpimg.wallstcn.com/e4558086-631c-425c-9430-56ffb46e70b3",
          importance: 3,
          pageviews: 2864,
          platforms: ["a-platform"],
          reviewer: "Sandra",
          status: "published",
          title: "O20190407135010000000001",
          type: "CN"
        },
        {
          timestamp: 1432179778664,
          author: "Charles",
          comment_disabled: true,
          content_short: "mock data",
          display_time: "1994-05-25 23:37:25",
          foods: "鸡翅、萝卜、牛肉、红烧大闸蟹、红烧鸡翅",
          id: 1,
          image_uri:
            "https://wpimg.wallstcn.com/e4558086-631c-425c-9430-56ffb46e70b3",
          importance: 3,
          pageviews: 2864,
          platforms: ["a-platform"],
          reviewer: "Sandra",
          status: "published",
          title: "O20190407135010000000001",
          type: "CN"
        },
        {
          timestamp: 1432179778664,
          author: "Charles",
          comment_disabled: true,
          content_short: "mock data",
          display_time: "1994-05-25 23:37:25",
          foods: "鸡翅、萝卜、牛肉、红烧大闸蟹、红烧鸡翅",
          id: 1,
          image_uri:
            "https://wpimg.wallstcn.com/e4558086-631c-425c-9430-56ffb46e70b3",
          importance: 3,
          pageviews: 2864,
          platforms: ["a-platform"],
          reviewer: "Sandra",
          status: "published",
          title: "O20190407135010000000001",
          type: "CN"
        },
        {
          timestamp: 1432179778664,
          author: "Charles",
          comment_disabled: true,
          content_short: "mock data",
          display_time: "1994-05-25 23:37:25",
          foods: "鸡翅、萝卜、牛肉、红烧大闸蟹、红烧鸡翅",
          id: 1,
          image_uri:
            "https://wpimg.wallstcn.com/e4558086-631c-425c-9430-56ffb46e70b3",
          importance: 3,
          pageviews: 2864,
          platforms: ["a-platform"],
          reviewer: "Sandra",
          status: "published",
          title: "O20190407135010000000001",
          type: "CN"
        },
        {
          timestamp: 1432179778664,
          author: "Charles",
          comment_disabled: true,
          content_short: "mock data",
          display_time: "1994-05-25 23:37:25",
          foods: "鸡翅、萝卜、牛肉、红烧大闸蟹、红烧鸡翅",
          id: 1,
          image_uri:
            "https://wpimg.wallstcn.com/e4558086-631c-425c-9430-56ffb46e70b3",
          importance: 3,
          pageviews: 2864,
          platforms: ["a-platform"],
          reviewer: "Sandra",
          status: "published",
          title: "O20190407135010000000001",
          type: "CN"
        },
        {
          timestamp: 1432179778664,
          author: "Charles",
          comment_disabled: true,
          content_short: "mock data",
          display_time: "1994-05-25 23:37:25",
          foods: "鸡翅、萝卜、牛肉、红烧大闸蟹、红烧鸡翅",
          id: 1,
          image_uri:
            "https://wpimg.wallstcn.com/e4558086-631c-425c-9430-56ffb46e70b3",
          importance: 3,
          pageviews: 2864,
          platforms: ["a-platform"],
          reviewer: "Sandra",
          status: "published",
          title: "O20190407135010000000001",
          type: "CN"
        }
      ];
      this.listLoading = false;
    }, 2000);
  },
  filters: {
    // 过滤时间
    parseTime(time, cFormat) {
      if (arguments.length === 0) {
        return null;
      }
      const format = cFormat || "{y}-{m}-{d} {h}:{i}:{s}";
      let date;
      if (typeof time === "object") {
        date = time;
      } else {
        if (typeof time === "string" && /^[0-9]+$/.test(time)) {
          time = parseInt(time);
        }
        if (typeof time === "number" && time.toString().length === 10) {
          time = time * 1000;
        }
        date = new Date(time);
      }
      const formatObj = {
        y: date.getFullYear(),
        m: date.getMonth() + 1,
        d: date.getDate(),
        h: date.getHours(),
        i: date.getMinutes(),
        s: date.getSeconds(),
        a: date.getDay()
      };
      const timeStr = format.replace(/{(y|m|d|h|i|s|a)+}/g, (result, key) => {
        let value = formatObj[key];
        // Note: getDay() returns 0 on Sunday
        if (key === "a") {
          return ["日", "一", "二", "三", "四", "五", "六"][value];
        }
        if (result.length > 0 && value < 10) {
          value = "0" + value;
        }
        return value || 0;
      });
      return timeStr;
    }
  }
}
```
* 效果图如下

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/4/18/16a2ef02a2cbed75~tplv-t2oaga2asx-image.image)
用法都是看GitHub开源项目的和博客的,自己本身还没有二次封装这样内容的实力,欢迎大佬提出宝贵的意见。

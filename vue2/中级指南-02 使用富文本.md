# 富文本
富文本是管理后台一个核心的功能，但同时又是一个有很多坑的地方。在选择富文本的过程中我也走了不少的弯路，市面上常见的富文本都基本用过了，最终权衡了一下选择了Tinymce。

这里在简述一下推荐使用 tinymce 的原因：tinymce 是一家老牌做富文本的公司(这里也推荐 ckeditor，也是一家一直做富文本的公司，新版本很不错)，它的产品经受了市场的认可，不管是文档还是配置的自由度都很好。在使用富文本的时候有一点也很关键就是复制格式化，之前在用一款韩国人做的富文本 summernote 被它的格式化坑的死去活来，但 tinymce 的去格式化相当的好，它还有一些增值服务(付费插件)，最好用的就是powerpaste，非常的强大，支持从 word 里面复制各种东西，而且还帮你顺带格式化了。富文本还有一点也很关键，就是拓展性。楼主用 tinymce 写了好几个插件，学习成本和容易度都不错，很方便拓展。最后一点就是文档很完善，基本你想得到的配置项，它都有。tinymce 也支持按需加载，你可以通过它官方的 build 页定制自己需要的 plugins。

**以上内容引自[vue-element-admin](https://panjiachen.github.io/vue-element-admin-site/zh/feature/component/rich-editor.html)作者官网**
# Tinymce的使用方法
目前采用全局引用的方式。代码地址：static/tinymce (static 目录下的文件不会被打包), 在 index.html 中引入。并确保它的引入顺序在你的app.js之前！
* 第一步 我们需要去官网下载他的源文件,或者引入在线地址
* 第二步 由于TinyMCE允许通过CSS选择器识别可替换元素，因此唯一的要求是传递包含selectorto 的对象tinymce.init()。

```
在这个例子中，替换<textarea id='mytextarea'>
通过使选择器与TinyMCE的5.0编辑器实例'#mytextarea'来tinymce.init()。
<!DOCTYPE html>
<html>
<head>
  <script src='https://cloud.tinymce.com/5/tinymce.min.js?apiKey=your_API_key'></script>
  <script>
  tinymce.init({
    selector: '#mytextarea'
  });
  </script>
</head>

<body>
<h1>TinyMCE Quick Start Guide</h1>
  <form method="post">
    <textarea id="mytextarea">Hello, World!</textarea>
  </form>
</body>
</html>
```
* 第三步 使用表单POST保存内容,当form被提交后，TinyMCE的5.0主编模仿一个普通的HTML行为textarea过程中POST。在用户的表单处理程序中，提交的内容可以与从常规创建的内容相同的方式处理textarea。
### 项目实战
由于目前使用 npm 安装 Tinymce 方法比较复杂而且还有一些问题(日后可能会采用该模式)且会大大增加编译的时间所以暂时不准备采用。所以这里直接去官网下载源文件保存在项目static文件夹中
#### 使用
由于富文本不适合双向数据流，所以只会 watch 传入富文本的内容一次变化，之后传入内容的变化就不会再监听了，如果之后还有改变富文本内容的需求。

可以通过 this.refs.xxx.setContent() 手动来设置。
* 官网下载[源文件](https://www.tiny.cloud/docs/quick-start/)放在static文件下如图

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/4/19/16a3356321b7a0f2~tplv-t2oaga2asx-image.image)
* 在index.html页面中引入 tinymce.min.js文件, 确保它的引入顺序在你的app.js之前！如图

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/4/19/16a335851214ab2a~tplv-t2oaga2asx-image.image)
* 在component文件夹内创建一个Tinymce文件夹用于封装我们要使用的富文本组件, 目录如图

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/4/19/16a336eeee6fd42d~tplv-t2oaga2asx-image.image)
其中两个js文件是对应的工具栏列表等其他插件功能, index.vue是封装好的模板,components中的组件是封装的图片上传的弹窗功能

plugins.js文件的代码如下
```
const plugins = ['advlist anchor autolink autosave code codesample colorpicker colorpicker
contextmenu directionality emoticons fullscreen hr image imagetools insertdatetime link
lists media nonbreaking noneditable pagebreak paste preview print save searchreplace
spellchecker tabfocus table template textcolor textpattern visualblocks visualchars wordcount']

export default plugins
```
toolbar.js文件的代码如下

```
const toolbar = ['searchreplace bold italic underline strikethrough alignleft aligncenter
alignright outdent indent  blockquote undo redo removeformat subscript superscript code
codesample', 'hr bullist numlist link image charmap preview anchor pagebreak insertdatetime
media table emoticons forecolor backcolor fullscreen']

export default toolbar
```
index.vue组件的代码如下

```
<template>
  <div :class="{fullscreen:fullscreen}" class="tinymce-container editor-container">
    <textarea :id="tinymceId" class="tinymce-textarea" />
    <div class="editor-custom-btn-container">
      <editorImage color="#1890ff" class="editor-upload-btn" @successCBK="imageSuccessCBK" />
    </div>
  </div>
</template>

<script>
// 导入图片上传的组件
import editorImage from './components/editorImage'
// 导入富文本插件
import plugins from './plugins'
// 导入富文本工具栏
import toolbar from './toolbar'

export default {
  name: 'Tinymce',
  components: { editorImage },
  props: {
    id: {
      type: String,
      default: function() {
        return 'vue-tinymce-' + +new Date() + ((Math.random() * 1000).toFixed(0) + '')
      }
    },
    value: {
      type: String,
      default: ''
    },
    toolbar: {
      type: Array,
      required: false,
      default() {
        return []
      }
    },
    menubar: {
      type: String,
      default: 'file edit insert view format table'
    },
    height: {
      type: Number,
      required: false,
      default: 360
    }
  },
  data() {
    return {
      hasChange: false,
      hasInit: false,
      tinymceId: this.id,
      fullscreen: false,
      languageTypeList: {
        'en': 'en',
        'zh': 'zh_CN'
      }
    }
  },
  computed: {
    language() {
      return 'zh_CN'
    }
  },
  watch: {
    value(val) {
      if (!this.hasChange && this.hasInit) {
        this.$nextTick(() =>
          window.tinymce.get(this.tinymceId).setContent(val || ''))
      }
    },
    language() {
      this.destroyTinymce()
      this.$nextTick(() => this.initTinymce())
    }
  },
  mounted() {
    this.initTinymce()
  },
  activated() {
    this.initTinymce()
  },
  deactivated() {
    this.destroyTinymce()
  },
  destroyed() {
    this.destroyTinymce()
  },
  methods: {
    initTinymce() {
      const _this = this
      window.tinymce.init({
        language: this.language,
        selector: `#${this.tinymceId}`,
        height: this.height,
        body_class: 'panel-body ',
        object_resizing: false,
        toolbar: this.toolbar.length > 0 ? this.toolbar : toolbar,
        menubar: this.menubar,
        plugins: plugins,
        end_container_on_empty_block: true,
        powerpaste_word_import: 'clean',
        code_dialog_height: 450,
        code_dialog_width: 1000,
        advlist_bullet_styles: 'square',
        advlist_number_styles: 'default',
        imagetools_cors_hosts: ['www.tinymce.com', 'codepen.io'],
        default_link_target: '_blank',
        link_title: false,
        nonbreaking_force_tab: true, // inserting nonbreaking space &nbsp; need Nonbreaking Space Plugin
        init_instance_callback: editor => {
          if (_this.value) {
            editor.setContent(_this.value)
          }
          _this.hasInit = true
          editor.on('NodeChange Change KeyUp SetContent', () => {
            this.hasChange = true
            this.$emit('input', editor.getContent())
          })
        },
        setup(editor) {
          editor.on('FullscreenStateChanged', (e) => {
            _this.fullscreen = e.state
          })
        }
        // 整合七牛上传
        // images_dataimg_filter(img) {
        //   setTimeout(() => {
        //     const $image = $(img);
        //     $image.removeAttr('width');
        //     $image.removeAttr('height');
        //     if ($image[0].height && $image[0].width) {
        //       $image.attr('data-wscntype', 'image');
        //       $image.attr('data-wscnh', $image[0].height);
        //       $image.attr('data-wscnw', $image[0].width);
        //       $image.addClass('wscnph');
        //     }
        //   }, 0);
        //   return img
        // },
        // images_upload_handler(blobInfo, success, failure, progress) {
        //   progress(0);
        //   const token = _this.$store.getters.token;
        //   getToken(token).then(response => {
        //     const url = response.data.qiniu_url;
        //     const formData = new FormData();
        //     formData.append('token', response.data.qiniu_token);
        //     formData.append('key', response.data.qiniu_key);
        //     formData.append('file', blobInfo.blob(), url);
        //     upload(formData).then(() => {
        //       success(url);
        //       progress(100);
        //     })
        //   }).catch(err => {
        //     failure('出现未知问题，刷新页面，或者联系程序员')
        //     console.log(err);
        //   });
        // },
      })
    },
    destroyTinymce() {
      const tinymce = window.tinymce.get(this.tinymceId)
      if (this.fullscreen) {
        tinymce.execCommand('mceFullScreen')
      }

      if (tinymce) {
        tinymce.destroy()
      }
    },
    setContent(value) {
      window.tinymce.get(this.tinymceId).setContent(value)
    },
    getContent() {
      window.tinymce.get(this.tinymceId).getContent()
    },
    imageSuccessCBK(arr) {
      const _this = this
      arr.forEach(v => {
        window.tinymce.get(_this.tinymceId).insertContent(`<img class="wscnph" src="${v.url}" >`)
      })
    }
  }
}
</script>

<style scoped>
.tinymce-container {
  position: relative;
  line-height: normal;
}
.tinymce-container>>>.mce-fullscreen {
  z-index: 10000;
}
.tinymce-textarea {
  visibility: hidden;
  z-index: -1;
}
.editor-custom-btn-container {
  position: absolute;
  right: 4px;
  top: 4px;
  /*z-index: 2005;*/
}
.fullscreen .editor-custom-btn-container {
  z-index: 10000;
  position: fixed;
}
.editor-upload-btn {
  display: inline-block;
}
</style>

```
图片上传组件editorImage的代码如下

```
<template>
  <div class="upload-container">
    <el-button :style="{background:color,borderColor:color}" icon="el-icon-upload" size="mini" type="primary" @click=" dialogVisible=true">
      上传图片
    </el-button>
    <el-dialog :visible.sync="dialogVisible">
      <el-upload
        :multiple="true"
        :file-list="fileList"
        :show-file-list="true"
        :on-remove="handleRemove"
        :on-success="handleSuccess"
        :before-upload="beforeUpload"
        class="editor-slide-upload"
        action="https://httpbin.org/post"
        list-type="picture-card"
      >
        <el-button size="small" type="primary">
          点击上传
        </el-button>
      </el-upload>
      <el-button @click="dialogVisible = false">
        取 消
      </el-button>
      <el-button type="primary" @click="handleSubmit">
        确 定
      </el-button>
    </el-dialog>
  </div>
</template>

<script>
// import { getToken } from 'api/qiniu'

export default {
  name: 'EditorSlideUpload',
  props: {
    color: {
      type: String,
      default: '#1890ff'
    }
  },
  data() {
    return {
      dialogVisible: false,
      listObj: {},
      fileList: []
    }
  },
  methods: {
    checkAllSuccess() {
      return Object.keys(this.listObj).every(item => this.listObj[item].hasSuccess)
    },
    handleSubmit() {
      const arr = Object.keys(this.listObj).map(v => this.listObj[v])
      if (!this.checkAllSuccess()) {
        this.$message('请等待所有图片上传成功 或 出现了网络问题，请刷新页面重新上传！')
        return
      }
      this.$emit('successCBK', arr)
      this.listObj = {}
      this.fileList = []
      this.dialogVisible = false
    },
    handleSuccess(response, file) {
      const uid = file.uid
      const objKeyArr = Object.keys(this.listObj)
      for (let i = 0, len = objKeyArr.length; i < len; i++) {
        if (this.listObj[objKeyArr[i]].uid === uid) {
          this.listObj[objKeyArr[i]].url = response.files.file
          this.listObj[objKeyArr[i]].hasSuccess = true
          return
        }
      }
    },
    handleRemove(file) {
      const uid = file.uid
      const objKeyArr = Object.keys(this.listObj)
      for (let i = 0, len = objKeyArr.length; i < len; i++) {
        if (this.listObj[objKeyArr[i]].uid === uid) {
          delete this.listObj[objKeyArr[i]]
          return
        }
      }
    },
    beforeUpload(file) {
      const _self = this
      const _URL = window.URL || window.webkitURL
      const fileName = file.uid
      this.listObj[fileName] = {}
      return new Promise((resolve, reject) => {
        const img = new Image()
        img.src = _URL.createObjectURL(file)
        img.onload = function() {
          _self.listObj[fileName] = { hasSuccess: false, uid: file.uid, width: this.width, height: this.height }
        }
        resolve(true)
      })
    }
  }
}
</script>

<style lang="scss" scoped>
.editor-slide-upload {
  margin-bottom: 20px;
  /deep/ .el-upload--picture-card {
    width: 100%;
  }
}
</style>

```
以上代码直接复制到本地就能够直接使用
* 接下来在需要使用富文本组件的地方引用tinymce文件下的index.vue文件即可,如图使用


![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/4/19/16a33a7758c67ce4~tplv-t2oaga2asx-image.image)
当form被提交后，TinyMCE的5.0主编模仿一个普通的HTML行为textarea过程中POST。在用户的表单处理程序中，提交的内容可以与从常规创建的内容相同的方式处理textarea。

这里定义了一个add方法 在富文本内编辑内容后,点击提交按钮即可获取到postForm.content的内容(富文本编辑的内容)

**当我们把富文本的内容传递后台后,如果有二次编辑的需求,后台又将数据原封不动的传递给前端,如何将传递回来的数据放置在富文本中呢?**

```
使用方式:
调用tinymce组件中的setContent()方法即可;
例如通过chage方法调用
change () {
      this.$refs.Tinymce.setContent("'<h1>我是后台传递回来的数据,要放在富文本中二次编辑内容<h1>'")
    }
```
封装的代码都是看vue-element-admin开源项目的,具体代码还在学习当中,欢迎提出宝贵意见

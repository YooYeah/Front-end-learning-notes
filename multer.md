### multer

##### *——express.js提供，用于处理multipart/form-data类型的上传文件*

**Installation：**

```
npm install --save multer
```
**Usage：**

客户端上传表单文件时，`enctype` 有三种取值，当取值为 `multipart/form-data ` 时，由express启动的服务获取的request无法取到该表单文件，Express.js为我们提供了Multer库，专门用于处理该类型的文件。

```
<form action="/profile" method="post" enctype="multipart/form-data">
  <input type="file" name="avatar" />
</form>
```

或使用JS：

```
// ev.dataTransfer.files可以取到文件
const form = new FormData();
files.forEach(file => {
  form.append("file", file);
})
```

server.js获取文件方法如下：

```
var express = require('express')
var multer  = require('multer')
var upload = multer({ dest: 'uploads/' })
var app = express()

app.post('/profile', upload.single('avatar'), function (req, res, next) {
  // req.file is the `avatar` file
  // req.body will hold the text fields, if there were any
})
app.post('/profile', upload.array('avatar'), function (req, res, next) {
  // req.files is array of `avatar` files
  // req.body will contain the text fields, if there were any
})
```

Multer为req添加了一个body对象以及一个file/files对象，body对象包含的是表单文本域的内容，如使用`form.append("arg","this is string")` ，那么`req.body={arg:"this is string"}` ，获取文本域内容可从body对象中获取；file/files对象即客户端上传的文件，post第二个参数取值参考[点这里](https://github.com/expressjs/multer/blob/master/README.md#multeropts)。`upload.single(name)` 及`upload.array(name)` 中的 `name` 都要与客户端的设置一样，如 `input` 标签中的 `name` 属性，或是 `append` 方法的第一个值。`upload.array(name)` 得到的是一个 `Express.Multer.File[] ` 类型的值（上传一个文件，长度为1；上传N个文件，长度为N）。每个file上提供如下API：

| Key            | Description                                   | Note            |
| -------------- | --------------------------------------------- | --------------- |
| `fieldname`    | Field name specified in the form              |                 |
| `originalname` | Name of the file on the user's computer       |                 |
| `encoding`     | Encoding type of the file                     |                 |
| `mimetype`     | Mime type of the file                         |                 |
| `size`         | Size of the file in bytes                     |                 |
| `destination`  | The folder to which the file has been saved   | `DiskStorage`   |
| `filename`     | The name of the file within the `destination` | `DiskStorage`   |
| `path`         | The full path to the uploaded file            | `DiskStorage`   |
| `buffer`       | A `Buffer` of the entire file                 | `MemoryStorage` |

若要保存上传的文件，可以配置 `DiskStorage` ：参见[这里](https://github.com/expressjs/multer/blob/master/README.md#diskstorage)

```
var storage = multer.diskStorage({
  destination: function (req, file, cb) {
    cb(null, '/tmp/my-uploads')
  },
  filename: function (req, file, cb) {
    //文件名不包含扩展名，若使用file.originalname，则使用文件原来的名字，包含后缀
    cb(null, file.fieldname + '-' + Date.now()) 
  }
})

var upload = multer({ storage: storage })
```

客户端上传文件，服务端获取文件并保存，文件路径可从 `file.destination` 或 `file.path` 获取。

**Refs：**

[express](https://expressjs.com/)

 [Multer](https://github.com/expressjs/multer/blob/master/README.md)

[Serving static files in Express](https://expressjs.com/en/starter/static-files.html)
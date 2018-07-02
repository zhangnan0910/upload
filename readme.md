# 上传文件

## 前端上传

##### 方法一
```
// name要跟single里的值匹配 
<form action="http://localhost:3000/api/upimg" method="POST"  enctype="multipart/form-data">
    <input type="file" name='img'>
    <input type="submit">
</form>
```
##### 方法二
html
```
    <input type="file" value=""/>
```
js
```
    // 创建一个FormData对象,用来组装一组用 XMLHttpRequest发送请求的键/值对
    var fd = new FormData();

    // 把 input 标签获取的文件加入 FromData 中
    fd.append('file', files[0]);

    // 请求后台接口 传入fd文件(方法1)
    this.$http.post('/api/upimg',Upimg).then(res=>{
            console.log(res)
        })

    // Ajax(方法2)
    var request = new XMLHttpRequest();
    request.open("POST", "http://localhost:5000/upload");
    request.send(fd);
    request.onreadystatechange = function(){
        if(request.readyState === 4 && request.status === 200){
            console.log("上传成功");
            var response = JSON.parse(request.responseText);
            console.log(response);
        }
    }

```
## 服务端
```
// 解析二进制
const multer = require('multer')

// 使用硬盘存储模式设置存放接收到的文件的路径以及文件名
var storage = multer.diskStorage({
    destination: function (req, file, cb) {
        // 接收到文件后输出的保存路径（若不存在则需要创建）
        cb(null, __dirname + '/upload');
    },
    filename: function (req, file, cb) {
        // 将保存文件名设置为 时间戳 + 文件原始名，比如 151342376785-123.jpg
        cb(null, Date.now() + "-" + file.originalname);
    }
});
// 创建 multer 对象
var upload = multer({ storage: storage });

// 上传图片
app.post('/api/upimg',upload.single('img'),(req,res)=>{
    // 成功返回前端数据
    res.json({
        code:1,
        // 图片
        url:'http://localhost:3000/upload/'+req.file.filename
    })
})
```

## 显示查询路径
```
http://localhost:3000/upload/1530532186536-123.png

```
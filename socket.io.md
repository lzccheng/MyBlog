# socket.io
[官网](https://socket.io)
 ##### 主要是解决轮询带来的资源浪费，通过长连接，主要以事件注册（socket.on）和事件触发（socket.emit）通信
 官网上有简单的demo：
 ```js
 //server.js
 var app = require('express')();
var http = require('http').createServer(app);
var io = require('socket.io')(http);

app.get('/', function(req, res){
  res.sendFile(__dirname + '/index.html');
});

 io.on('connection', function(socket){  //建立连接时触发
  console.log('a user connected');
  socket.on('disconnect', function(){  //断开连接时触发
    console.log('user disconnected');
  });
  socket.on('chat message', function(msg){  //定义server事件，由client触发
    io.emit('chat message', msg);  //触发cliet事件
  });
});

http.listen(3000, function(){
  console.log('listening on *:3000');
});

 //client.js
 <script>
  $(function () {
    var socket = io();
    $('form').submit(function(e){
      e.preventDefault(); // prevents page reloading
      socket.emit('chat message', $('#m').val());  //触发server事件
      $('#m').val('');
      return false;
    });
    socket.on('chat message', function(msg){  //定义client事件
      $('#messages').append($('<li>').text(msg));
    });
  });
</script>
```
以上demo是消息的发送，下面介绍两种图片的发送
###### 1、使用FileReader把图片文件转成base64，当作信息发送到server，再由server分发到其他client
```js
//client.js
$('#pic').change((e) => {
	const file = e.target.files[0]
	const reader = new FileReader()
	reader.readAsDataURL(file)
	reader.onload = function() {
	    socket.emit('sendTu', { img: this.result })  //this.result  ：base64发送到server
	}
}
//server.js
socket.on('sendTu', msg => {  //接收base64
    msg.id = socket.id
    socket.emit('renderTu', msg)  //分发给自己
    socket.broadcast.emit('renderTu', msg)  //分发到其他client
})
 ```
 ##### 2、使用ajax发送文件到server，再由server分发图片地址，使用到FormData
 ```js
 //client.js
 const post = (url, data, cb) => {
     const req =new XMLHttpRequest()
     req.open('POST', url, true)
     req.send(data)
     req.onreadystatechange = () => {
         if(req.readyState == 4) {
             if((req.status  >= 200 && req.status  < 300) || req.status  == 304) {
                 console.log('succeess', req.responseText)
                 typeof cb === 'function' && cb(req.responseText)
             }
         }
     }
 }
$('#pic').change((e) => {
	 const file = e.target.files[0]
     const formData = new FormData()
     formData.append(file.name, file)
     post('/sendPic', formData, res => {
         const data = JSON.parse(res)
         socket.emit('sendTu', { img: data.path })  //接收到路径，通知server进行分发地址
     })
}
//server.js  //需要用到formidable模块，npm安装即可
app.use(express.static(__dirname + '/static'))  //设置static为静态文件目录
app.post('/sendPic', (req, res) => {
    console.log('sendPic')
    let imgname = null;
    let form = new formidable.IncomingForm();
    form.uploadDir = './static/images';  //保存到static/images目录下
    form.parse(req, (err, fields, files) => {
        res.send(imgname);  //存储成功把路径返回
    });
    form.on('fileBegin', (name, file) => {
        file.path = path.join(__dirname, `./static/images/${file.name}`);
        imgname = { name: file.name, path: `/images/${file.name}` };  
    })
})
```


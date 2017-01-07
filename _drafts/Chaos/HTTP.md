# HTTP #

## GET\POST ##

GET把参数包含在URL中，POST通过request body传递参数

GET在浏览器回退时是无害的，而POST会再次提交请求

GET产生的URL地址可以被Bookmark，而POST不可以

GET请求会被浏览器主动cache，而POST不会，除非主动设置

GET请求只能进行url编码，而post支持多种编码方式

GET请求参数会被完整保留在浏览器历史记录里，而POST中的参数不会被保留

GET请求在URL传送的参数是由长度限制的，而POST没有

对参数的数据类型，GET只接受ASCII字符，而POST没有限制

GET比POST更不安全，因为参数直接暴露在URL上，所以不能用来传递敏感信息

GET参数通过URL传递，POST放在Request body中

### 拓展 ###
HTTP的底层是TCP/IP，所以get和post的底层也是。也就是说，get和post能做的事情是一样一样的，给get加上request body，给post加上url参数，技术上是完全可行的。

HTTP只是一个行为准则，而TCP才是get和post怎么实现的基本。

业界不成文的规定，浏览器通常会限制url长度在2K个字节，而服务器最多处理64K大小的URL，超过的部分，恕不处理。所以虽然get可以带request body，但是也不能保证一定被接收到。

### 隐藏区别 ###
GET产生一个TCP数据包，POST产生两个TCP数据包

- 对于get，浏览器会把http header和data一并发送出去，服务器响应200
- 对于post，浏览器先发送header，服务器响应100 continue，浏览器再发送data，服务器响应200 ok

但是firefox值发送一次，对POST并非所有浏览器都发送两次



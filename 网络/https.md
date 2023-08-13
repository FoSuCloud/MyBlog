## https
* https是基于TLS/SSL协议实现的HTTP协议。在node.js中是作为一个单独的模块的
* http和https的区别
* http协议使用`明文传输数据`，当涉及到敏感信息的传输时，很容易受到窃听或者中间人攻击
* 而https是基于TLS/SSL的http协议，使用`加密通讯`的手段和网络服务器鉴定的方式来进行信息的安全传输

## tls/ssl协议
1）tls/ssl协议作用：
* https协议是http报文直接发给tls层加密，tls加密完成后，把加密后的报文发送给tcp套接字socket
* socket再把加密后的套接字发送给目标服务器
---
* socket接收到服务器响应报文后，先把报文发送给tls层解密，tls解密完成后再发送给对应的进程

2）tls/ssl协议的连接过程
* tls/ssl协议的基本思路是采用公钥加密法，也就是`客户端先向服务器请求公钥，然后用公钥加密信息，服务器接收到公钥发送过来的信息，用自己的私钥解密`
* 存在的问题是：怎么保证服务器的公钥正确性？
* `只要服务器发送过来的数字证书是正确的，那么浏览器就会认为公钥是正确的，可信的`
---
* tls/ssl握手阶段的连接建立主要分为四个阶段
1. 客户端发出请求，提供以下信息
* 向服务器提供支持的tls版本
* `一个客户端生成的随机数`
* `支持的加密方法`
* `支持的压缩算法`
2. 服务器回应，提供以下信息
* 确认tls版本，如果不一致则关闭连接
* `服务器也生成一个随机数`
* `根据客户端支持的加密方法，确认一个加密方法`
* `服务器证书`
3. 客户端回应
* 首先需要先确认服务器发送过来的数字证书是否可信，是否过期。如果不符合条件则提示警告
* 如果证书没问题，或者用户确认要继续访问网站，那么就给服务器发送以下消息
* （`注意以下消息都是根据证书里面的服务器公钥进行加密的`）
* `生成第三个随机数！使用服务器公钥加密，防止被窃听`
* 编码改变通知，表示随后的消息都将使用双方约定的加密方法和密钥进行加密
* 客户端握手结束通知
---
* `第三个随机数是为了保证协商出来的密钥的随机性，三个伪随机更加接近随机性～`
4. 服务器回应
* `服务器根据三个的随机数，计算生成本次会话使用的会话密钥`
* 然后向客户端发送以下消息
* 编码改变通知，表示之后的消息（加密通信过程）都将使用双方约定的加密方法和密钥发送
* 服务器握手结束通知，表示服务器的握手阶段结束
---
* `注意，是客户端和服务器各自利用三个随机数，根据事先约定好的加密方法生成密钥（注意生成的肯定是同一个密钥！）`
---
第一次：C ===》随机数  ==》 S  （两者都有同一个随机数）
第二次：S ===> 随机数  ==》 C  (两者都有两个随机数)
`注意上面两次都是不安全的，随机数都可以被中间人劫持，但是没事，因为连接还没建立`
第三次：C ===》服务器公钥加密第三个随机数  ===》 服务器 (两者都有三个随机数)
第四次：S ===> C  (通知连接建立完成)
`服务器公钥是放在数字证书中的，数字证书理论上也会被劫持，但是没事，浏览器会识别证书的可信度，不可信则不会建立连接`
---
* `由于加密通信过程中使用的是同一个密钥（没有公钥和私钥之分，只是一个密钥），所以加密通信过程是对称加密`
---
* `握手阶段的前两步，也就是客户端的验证数字证书这一阶段，使用的是非对称加密！`
* `非对称加密和对称加密的区别就在于，非对称加密是使用公钥加密，使用私钥解密；或者使用私钥加密，使用公钥解密（数字证书认证是第二种）`
---
* 数字证书认证流程：
1. 首先服务器把公钥发送给ca机构
2. ca机构用自己的`私钥将服务器的公钥加密`并颁发给数字证书,把数字证书发给服务器
3. `然后服务器才把数字证书发送给客户端`
4. `客户端拿到数字证书后，使用（浏览器内置的）ca机构的公钥进行数字签名的认证（解密），认证成功才返回公钥给浏览器`
* [数字证书认证]("https://www.cnblogs.com/fengf233/p/11775415.html")

3）tls和ssl关系
* 最初的时候，使用的是ssl协议，到了ssl3.0版本，这也是应用比较广泛的版本
* 后来因为维护的公司改变了，对 `ssl协议进行了升级，产生了tls1.0版本，这也是目前应用最广泛的版本`
* 另外现在大多数浏览器都支持tls1.2版本，1.3版本很少支持

* [tls/ssl握手参考]('https://www.ruanyifeng.com/blog/2014/02/ssl_tls.html)

### 数字证书
* [参考]("https://www.ruanyifeng.com/blog/2011/08/what_is_a_digital_signature.html")

## https例子
```javascript
// 前端,需要在vue项目开启https，开启代理
fetch('https://localhost:3000').then((res)=>{
    console.log(res)
})
// 后端
const https = require('https')
const fs= require('fs')
const path = require('path')

let serve = https.createServer({
    key: fs.readFileSync(path.join(__dirname,'./ssl/key.pem')),
    cert: fs.readFileSync(path.join(__dirname,'./ssl/ca.pem'))
},(req,res)=>{
    let arr=['http://localhost:63342','https://localhost:3001']
    if(arr.includes(req.headers.origin)){
        res.setHeader('content-type','text/html; charset=utf-8')
        res.setHeader('Cache-Control','no-cache')
        res.setHeader('Connection','keep-alive')
        res.setHeader('Access-Control-Allow-Origin',req.headers.origin) // req.headers.origin
        res.setHeader('Access-Control-Allow-Credentials', 'true')
    }
    res.end('hello https')
})

serve.listen(3000)
```
* `我们直接启动前端项目，后端项目，然后在前端项目中执行请求，会发现提示：ERR_CERT_INVALID`
* 这是因为浏览器发现这个证书不可信，`解决方法：先访问后端端口，在浏览器直接输入thisisunsafe`
* 然后再访问前端项目端口就可以接收到该网站的响应了！（因为浏览器就认为我们知道不可信，风险是我门可以承担的）

#### 证书验证
https://www.trustasia.com/tools#ssl-tools

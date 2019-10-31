### 一、Http状态码
+ 304表示请求的资源并没有被修改，可以直接使用浏览器中的缓存的版本.

### 二、HTTP头部信息 
+ Content-Type: application/x-www-form-urlencoded(default)
  
### 三、请求方式
+ get请求
   1. 查询字符串中每个参数的名称和值都必须使用 encodeURIComponent()进行编码，然后才能放到 URL 的末尾；而且所有 **名-值对** 都必须由和号（&）分隔
+ post请求  
   1. POST 请求的主体可以包含非常多的数据， 而且格式不限。
   2. FormData 为序列化表单以及创建与表单格式相同的数据（用于通过 XHR传输）提供 了便利。
+  GET 请求相比，POST 请求消耗的资源会更多一些。从性能角度来看，以发送 相同的数据计，GET 请求的速度最多可达到 POST 请求的两倍   
### 四、跨域资源共享-Cross-Origin Resource Sharing
  + 什么是跨域
      
    例如有个前端js资源从a站点加载，而利用XHR从B站点请求一个json文件
    

  + CORS背后的基本思想，就是使用自定义的 HTTP头部 让浏览器与服务器进行沟通，从而决定请求或响应是应该成功，还是应该失败。
  
    比如一个简单的使用 GET 或 POST 发送的请求，它没有自定义的头部，而主体内容是 text/plain。在 发送该请求时，需要给它附加一个额外的 Origin 头部，其中包含请求页面的源信息（协议、域名和端 口），以便服务器根据这个头部信息来决定是否给予响应。下面是 Origin 头部的一个示例： Origin: http://www.nczonline.net 
    如果服务器认为这个请求可以接受，就在 Access-Control-Allow-Origin 头部中回发相同的源 信息（如果是公共资源，可以回发"*"）。例如： 
    
    Access-Control-Allow-Origin: http://www.nczonline.net 
    如果没有这个头部，或者有这个头部但源信息不匹配，浏览器就会驳回请求。正常情况下，浏览器 会处理请求。注意，请求和响应都不包含 cookie信息。 

 + Preflighted Reqeusts 

   CORS通过一种叫做 Preflighted Requests的透明服务器验证机制支持开发人员使用自定义的头部、 GET或 POST之外的方法，以及不同类型的主体内容。在使用下列高级选项来发送请求时，就会向服务 器发送一个 Preflight请求。这种请求使用 OPTIONS方法，发送下列头部。 

   
   - Origin：与简单的请求相同。 
   - Access-Control-Request-Method：请求自身使用的方法。 
   - Access-Control-Request-Headers：（可选）自定义的头部信息，多个头部以逗号分隔。 以下是一个带有自定义头部 NCZ的使用 POST 方法发送的请求。 
    Origin: http://www.nczonline.net Access-Control-Request-Method: POST Access-Control-Request-Headers: NCZ 
  发送这个请求后，服务器可以决定是否允许这种类型的请求。服务器通过在响应中发送如下头部与 浏览器进行沟通
    - Access-Control-Allow-Origin：与简单的请求相同。 
    - Access-Control-Allow-Methods：允许的方法，多个方法以逗号分隔。
    - Access-Control-Allow-Headers：允许的头部，多个头部以逗号分隔。
    - Access-Control-Max-Age：应该将这个 Preflight请求缓存多长时间（以秒表示）。 
+ 带凭据的请求 

    默认情况下，跨源请求不提供凭据（cookie、HTTP 认证及客户端 SSL 证明等）。通过将 withCredentials 属性设置为 true，可以指定某个请求应该发送凭据。如果服务器接受带凭据的请 求，会用下面的 HTTP头部来响应。 
 
    Access-Control-Allow-Credentials: true 
    如果发送的是带凭据的请求，但服务器的响应中没有包含这个头部，那么浏览器就不会把响应交给 JavaScript（于是，responseText 中将是空字符串，status 的值为 0，而且会调用 onerror()事件处 理程序）。另外，服务器还可以在 Preflight响应中发送这个 HTTP头部，表示允许源发送带凭据的请求。

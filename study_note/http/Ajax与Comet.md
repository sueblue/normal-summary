### Ajax的核心对象-XHR(XMLHttpRequest)
1. 使用场景-与服务器互动而不需要刷新页面.需要长连接,则考虑websocket
```
function reqListener () {
  console.log(this.responseText);
}

var oReq = new XMLHttpRequest();
oReq.addEventListener("load", reqListener);

//open只是启动一个请求准备发送

oReq.open("GET", "http://www.example.org/example.txt");
oReq.send(null);

if ((oReq.status >= 200 && oReq.status < 300) || oReq.status == 304){      
    alert(oReq.responseText); } 
else {     
    alert("Request was unsuccessful: " + oReq.status); } 
 
```
+ 收到相应后,填充XHR对象的属性:
  1. responseText：作为响应主体被返回的文本。 
  2.  responseXML：如果响应的内容类型是"text/xml"或"application/xml"，这个属性中将保 存包含着响应数据的 XML DOM文档
  3. status：响应的 HTTP状态。 
  4. statusText：HTTP状态的说明。 
2. 进度事件 
  + loadstart：在接收到响应数据的第一个字节时触发。 
  + progress：在接收响应期间持续不断地触发。 
  + error：在请求发生错误时触发。 
  + abort：在因为调用 abort()方法而终止连接时触发。 
  + load：在接收到完整的响应数据时触发。 
  + loadend：在通信完成或者触发 error、abort 或 load 事件后触发。 
  + 每个请求都从触发 loadstart 事件开始，接下来是一或多个 progress 事件，然后触发 error、 abort 或 load 事件中的一个，后以触发 loadend 事件结束。 
### Comet-服务器推送
  1. 有两种实现 Comet的方式：长轮询和流
     + 长轮询--页面发起一个到服务器的请求，然后服务器一直保持连接打开，直到 有数据可发送。发送完数据之后，浏览器关闭连接，随即又发起一个到服务器的新请求。这一过程在页面打开期间一直持续不断。

     + 流不同于轮询，因为它在页面的整个生命周期内只使用一个 HTTP连接。具体来说，就是浏览器向服务器发送一个请求，而服务器保持连接打开，然后周 期性地向浏览器发送数据。
```
所有服务器端语言都支持打印到输出缓存然后刷新（将输出缓存中的内容一次性全部发送到客户 端）的功能。而这正是实现 HTTP流的关键所在

通过侦听 readystatechange 事件及检测 readyState 的值是否为 3，就可以利用 XHR 对象实现 HTTP 流。

随着不断从服务器接收数据，readyState 的值会周期性地变为 3。当 readyState 值变为 3时，responseText 属性中就会保 存接收到的所有数据。此时，就需要比较此前接收到的数据，决定从什么位置开始取得新的数据。
```
```
function createStreamingClient(url, progress, finished){ 
 
    var xhr = new XMLHttpRequest(),         
    received = 0; 
    xhr.open("get", url, true);     
    xhr.onreadystatechange = function(){         
        var result; 
        if (xhr.readyState == 3){ 
 
            //只取得最新数据并调整计数器             
            result = xhr.responseText.substring(received);     received += result.length; 
 
            //调用 progress 回调函数             
            progress(result); 
        } else if (xhr.readyState == 4){             
            finished(xhr.responseText);} 
 
    };     
    xhr.send(null);     
    return xhr;} 
 
var client = createStreamingClient("streaming.php", function(data){                 
    alert("Received: " + data);              }, 
    function(data){                
         alert("Done!");});
```
### SSE-Server-Sent Events，服务器发送事件
    SSE API 用于创建到服务器的单向连接，服务器通过这个连接可以发送任意数量的数据。服务器响应的 MIME 类型必须是 text/event-stream，而且是浏览器中的 JavaScript API 能解析格式输出。SSE 支持短轮 询、长轮询和 HTTP流，而且能在断开连接时自动确定何时重新连接
### Web Sockets 
    Web Sockets的目标是在一个单独的 持久连接上提供全双工、双向通信。在 JavaScript中创建了 Web Socket之后，会有一个 HTTP请求发送 到浏览器以发起连接。在取得服务器响应后，建立的连接会使用 HTTP 升级从 HTTP 协议交换为 Web Socket 协议

    由于 Web Sockets使用了自定义的协议，所以 URL模式也略有不同。未加密的连接不再是 http://， 而是 ws://；加密的连接也不是 https://，而是 wss://。在使用 Web Socket URL时，必须带着这个 模式，因为将来还有可能支持其他模式
    
    使用自定义协议而非 HTTP协议的好处是，能够在客户端和服务器之间发送非常少量的数据，而不 必担心 HTTP那样字节级的开销。由于传递的数据包很小，因此 Web Sockets非常适合移动应用。毕竟 对移动应用而言，带宽和网络延迟都是关键问题。使用自定义协议的缺点在于，制定协议的时间比制定 JavaScript API的时间还要长.

### SSE与webSockets的比较
面对某个具体的用例，在考虑是使用 SSE还是使用 Web Sockets时，可以考虑如下几个因素。
+ 首先， 你是否有自由度建立和维护 Web Sockets服务器？因为 Web Socket协议不同于 HTTP，所以现有服务器 不能用于 Web Socket通信。SSE倒是通过常规 HTTP通信，因此现有服务器就可以满足需求。 
+ 第二个要考虑的问题是到底需不需要双向通信。如果用例只需读取服务器数据（如比赛成绩），那 么 SSE 比较容易实现。如果用例必须双向通信（如聊天室），那么 Web Sockets 显然更好。别忘了，在 不能选择 Web Sockets的情况下，组合 XHR和 SSE也是能实现双向通信的。 
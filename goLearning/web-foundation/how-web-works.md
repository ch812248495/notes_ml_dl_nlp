 

**How web works**

brower is a client -&gt; enter the url -&gt; request for DNS analysis -&gt; get the ip -&gt; find the corresponding server -&gt; build the TCP connection -&gt; send the HTTP request -&gt; server start to dispose the packet -&gt; return the HTTP response -&gt; client start to render the body of the response -&gt; after receiving all contents disconnect the TCP

**URL and DNS analysis**

Here is the scheme of URL\(uniform resource locator\)

```
scheme://host[:port#]/path/.../[?query-string][#anchor]
scheme         指定底层使用的协议(例如：http, https, ftp)
host           HTTP服务器的IP地址或者域名
port#          HTTP服务器的默认端口是80，这种情况下端口号可以省略。如果使用了别的端口，必须指明，例如 http://www.cnblogs.com:8080/
path           访问资源的路径
query-string   发送给http服务器的数据
anchor         锚
```

DNS\(Domain Name System\)

From url to IP address:![](/assets/0.png)More detailed DNS analysis:

1. After entering www.qq.com, Check the local host file, if there is a mapping relation, finish the analysis
2. If no, check the local DNS buffer
3. If both not, check teh TCP/IP setting's prefered DNS server\(local DNS server\), this server will search and response to the query request.
4. If the local DNS server is not responsible for the analysis, but it has buffered the mapping relation, return this result.
5. If the local DNS cannot analyze this request, then query according to the setting of th local DNS server\(repeater\), if no repeater, send the DNS request to root DNS server, the root DNS server will judge who is responsible for managing this domain name, and return one IP. The local DNS server
6. if the reperter mode is activated, this DNS server will repeate the request to the upper level  

![](/assets/75.png)

**HTTP protocol**

port: 80

stateless, demand colokie mechanism

**HTTP request**

The structure of request packet

```
GET /domains/example/ HTTP/1.1		//请求行: 请求方法 请求URI HTTP协议/协议版本
Host：www.iana.org				//服务端的主机名
User-Agent：Mozilla/5.0 (Windows NT 6.1) AppleWebKit/537.4 (KHTML, like Gecko) Chrome/22.0.1229.94 Safari/537.4			//浏览器信息
Accept：text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8	//客户端能接收的mine
Accept-Encoding：gzip,deflate,sdch		//是否支持流压缩
Accept-Charset：UTF-8,*;q=0.5		//客户端字符编码集
//空行,用于分割请求头和消息体
//消息体,请求资源参数,例如POST传递的参数
```

the first line is request line, the next part is request header, the third part is body

Four fundamental methods to interact with the server:

```
GET    //query
POST    //modify
PUT    //add
DELETE    //delete
```

The difference between POST and GET![](/assets/get.png)get![](/assets/74.png)post

get 是幂等的, the data it post will be adhered right after the url. The length of the data it post is limited by the browser and the support of the os.\(the limitation os exert is on the length of the whole url\)

```
以?分割URL和传输数据，参数之间以&相连，如：login.action?name=hyddd&password=idontknow&verify=%E4%BD%A0%E5%A5%BD。
如果数据是英文字母/数字，原样发送，如果是空格，转换为+，
如果是中文/其他字符，则直接把字符串用BASE64加密，得出如：%E4%BD%A0%E5%A5%BD，其中％XX中的XX为该符号以16进制表示的ASCII。
```

```
幂等有一下几种定义：
　　对于单目运算，如果一个运算对于在范围内的所有的一个数多次进行该运算所得的结果和进行一次该运算所得的结果是一样的，那么我们就称该运算是幂等的。比如绝对值运算就是一个例子，在实数集中，有abs(a)=abs(abs(a))。
　　对于双目运算，则要求当参与运算的两个值是等值的情况下，如果满足运算结果与参与运算的两个值相等，则称该运算幂等，如求两个数的最大值的函数，有在在实数集中幂等，即max(x,x) = x。
```

post may change the resources on the server, the data it sends is on the body, which is more secure since your browsing history may leak.

**HTTP response packet**

The scheme of the response packet:

```
HTTP/1.1 200 OK						//状态行
Server: nginx/1.0.8					//服务器使用的WEB软件名及版本
Date:Date: Tue, 30 Oct 2012 04:14:25 GMT		//发送时间
Content-Type: text/html				//服务器发送信息的类型
Transfer-Encoding: chunked			//表示发送HTTP包是分段发的
Connection: keep-alive				//保持连接状态
Content-Length: 90					//主体内容长度
//空行 用来分割消息头和主体
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN"... //消息体
```

status code:

* 1XX: received and dispoing
* 2XX: received, known and accepted
* 3XX: redirected
* 4XX: client error: the request may possess some syntax errors or the request cannot be realized.
* 5XX: server error: the request is correct but the server cannot realize it.






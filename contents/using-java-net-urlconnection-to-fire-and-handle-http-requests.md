##如何使用java.net.URLConnection接收及发送HTTP请求

首先声明，下面的代码，都是基本的例子。更严谨的话，还应加入处理各种异常的代码(如IOExceptions、NullPointerException、ArrayIndexOutOfBoundsException)

###准备
首先，需要设置请求的URL以及charset(编码)；额外的参数，则取决于各自url的要求。
```java
String url = "http://example.com";
String charset = "UTF-8";
String param1 = "value1";
String param2 = "value2";
// ...
String query = String.format("param1=%s&param2=%s", 
     URLEncoder.encode(param1, charset), 
     URLEncoder.encode(param2, charset));
```
url中附带的请求参数，必须是name=value这样的格式，每个参数间用&连接。一般来说，你还得用 [URLEncoder#encode()](http://docs.oracle.com/javase/6/docs/api/java/net/URLEncoder.html)对参数做[编码](http://en.wikipedia.org/wiki/Percent-encoding)

上面例子还用到了String#format()。字符拼接方式，看个人喜好，我更喜欢用这个方式。

###发送一个[HTTP GET](http://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html#sec9.3)请求（可选：带上参数）
这依然是个繁琐的事情。默认的方式如下：
```java
URLConnection connection = new URL(url + "?" + query).openConnection();
connection.setRequestProperty("Accept-Charset", charset);
InputStream response = connection.getInputStream();
```
url和参数之间，要用？号连接。请求头（header）中的[Accept-Charset](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.2)，用于告诉服务器，你所发送参数的编码。如果你不带送任何参数，也可以不管Accept-Charset。另外如果你无需设置header，也可以用[URL#openStream()](http://docs.oracle.com/javase/6/docs/api/java/net/URL.html#openStream%28%29) 而非openConnection。
不管那种方式，假设服务器端是 [HttpServlet](http://docs.oracle.com/javaee/6/api/javax/servlet/http/HttpServlet.html)，那么你的get请求将会触发它的doGet()方法，它能通过[HttpServletRequest#getParameter()](http://docs.oracle.com/javaee/6/api/javax/servlet/ServletRequest.html#getParameter%28java.lang.String%29)获取你传递的参数。

###发送一个[HTTP POST](http://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html#sec9.5)请求，并带上参数
设置[URLConnection#setDoOutput()](http://docs.oracle.com/javase/6/docs/api/java/net/URLConnection.html#setDoOutput%28boolean%29)，等于隐式地将请求方法设为POST。标准的HTTP POST 表单，其Content-Tyep为application/x-www-form-urlencoded，请求的内容放到到body中。也就是如下代码：
```java
URLConnection connection = new URL(url).openConnection();
connection.setDoOutput(true); // Triggers POST.
connection.setRequestProperty("Accept-Charset", charset);
connection.setRequestProperty("Content-Type", "application/x-www-form-urlencoded;charset=" + charset);

try (OutputStream output = connection.getOutputStream()) {
    output.write(query.getBytes(charset));
}

InputStream response = connection.getInputStream();
```

提醒：

当你要提交一个HTML表单时，务必要把`<input type="hidden"`,`<input type="submit">`这类元素的值，也以name=value的形式提交，因为，服务端通常也需要这个信息，已确认哪一个按钮触发了这个提交动作。

也可以使用[HttpURLConnection](http://docs.oracle.com/javase/6/docs/api/java/net/HttpURLConnection.html) 来代替[URLConnection](http://docs.oracle.com/javase/6/docs/api/java/net/URLConnection.html) ，然后调用[HttpURLConnection#setRequestMethod()](http://docs.oracle.com/javase/6/docs/api/java/net/HttpURLConnection.html#setRequestMethod%28java.lang.String%29)来将请求设为POST类型。

```java
HttpURLConnection httpConnection = (HttpURLConnection) new URL(url).openConnection();
httpConnection.setRequestMethod("POST");
```

同样的，如果服务端是[HttpServlet](http://docs.oracle.com/javaee/6/api/javax/servlet/http/HttpServlet.html),将会触发它的[doPost()](http://docs.oracle.com/javaee/6/api/javax/servlet/http/HttpServlet.html#doPost%28javax.servlet.http.HttpServletRequest,%20javax.servlet.http.HttpServletResponse%29)方法,可以通过[HttpServletRequest#getParameter()](http://docs.oracle.com/javaee/6/api/javax/servlet/ServletRequest.html#getParameter%28java.lang.String%29)获取post参数

###触发HTTP请求的发送
你可以显式地通过[URLConnection#connect()](http://docs.oracle.com/javase/6/docs/api/java/net/URLConnection.html#connect%28%29)来发送请求，但是，当你调用获取响应信息的方法时，一样将自动发送请求。例如当你使用[URLConnection#getInputStream()](http://docs.oracle.com/javase/6/docs/api/java/net/URLConnection.html#getInputStream%28%29)时，就会自动触发请求，因此，connect()方法往往都是多余的。上面我的例子，也都是直接调用getInputStream()方法。

获取HTTP响应信息
1. HTTP响应码：
首先默认你使用了 [HttpURLConnection](http://docs.oracle.com/javase/6/docs/api/java/net/HttpURLConnection.html)
```java
int status = httpConnection.getResponseCode();
```
2. HTTP 响应头（headers)
```java
for (Entry<String, List<String>> header : connection.getHeaderFields().entrySet()) {
    System.out.println(header.getKey() + "=" + header.getValue());
}
```
3. HTTP响应编码：
当Content-Type中包含charset参数时，说明响应内容是基于charset参数指定的编码。因此，解码响应信息时，也要按照这个编码格式来。

```java
String contentType = connection.getHeaderField("Content-Type");
String charset = null;

for (String param : contentType.replace(" ", "").split(";")) {
    if (param.startsWith("charset=")) {
        charset = param.split("=", 2)[1];
        break;
    }
}

if (charset != null) {
    try (BufferedReader reader = new BufferedReader(new InputStreamReader(response, charset))) {
        for (String line; (line = reader.readLine()) != null;) {
            // ... System.out.println(line) ?
        }
    }
}
else {
    // It's likely binary content, use InputStream/OutputStream.
}
```


###session的维护
服务端session，通常是基于cookie实现的。你可以通过[CookieHandlerAPI](http://docs.oracle.com/javase/8/docs/api/java/net/CookieHandler.html)来管理cookie。在发送HTTP请求前，初始化一个[CookieManager](http://docs.oracle.com/javase/6/docs/api/java/net/CookieManager.html)， 然后设置参数为[CookiePolicy](http://docs.oracle.com/javase/6/docs/api/java/net/CookiePolicy.html).[CCEPT_ALL](http://docs.oracle.com/javase/6/docs/api/java/net/CookiePolicy.html#ACCEPT_ALL)。
```java
// First set the default cookie manager.
CookieHandler.setDefault(new CookieManager(null, CookiePolicy.ACCEPT_ALL));
// All the following subsequent URLConnections will use the same cookie manager.
URLConnection connection = new URL(url).openConnection();
// ...
connection = new URL(url).openConnection();
// ...
connection = new URL(url).openConnection();
// ...
```

请注意，这个方式并非适用于所有场景。如果使用这个方式失败了，你可以尝试自己设置cookie：你需要从响应头中拿到Set-Cookie参数，然后再把cookie设置到接下来的其他请求中。
```java
// Gather all cookies on the first request.
URLConnection connection = new URL(url).openConnection();
List<String> cookies = connection.getHeaderFields().get("Set-Cookie");
// ...

// Then use the same cookies on all subsequent requests.
connection = new URL(url).openConnection();
for (String cookie : cookies) {
    connection.addRequestProperty("Cookie", cookie.split(";", 2)[0]);
}
// ...
```
上面的split(";", 2)[0],作用是去掉一些跟服务端无关的cookie信息（例如expores，path等）。也可用cookie.substring(0, cookie.indexOf(';'))达到同样的目的

###流的处理
不管你是否通过connection.setRequestProperty("Content-Length", contentLength)方法，为content设置了定长，  [HttpURLConnection](http://docs.oracle.com/javase/6/docs/api/java/net/HttpURLConnection.html)在发送请求前，默认都会缓存整个请求的body。如果发送一个比较大的post请求（例如上传文件），有可能会导致OutOfMemoryException。为了避免这个问题，可以设置[HttpURLConnection#setFixedLengthStreamingMode()](http://docs.oracle.com/javase/6/docs/api/java/net/HttpURLConnection.html#setFixedLengthStreamingMode%28int%29)
```java
httpConnection.setFixedLengthStreamingMode(contentLength);
```
但如果content长度是未知的，则可以用[HttpURLConnection#setChunkedStreamingMode()](http://docs.oracle.com/javase/6/docs/api/java/net/HttpURLConnection.html#setChunkedStreamingMode%28int%29)。这样，header中Transfer-Encoding会变成chunked，你的请求将会分块发送，例如下面的例子，请求的body，将会按1KB一块，分块发送
```java
httpConnection.setChunkedStreamingMode(1024);
```

###User-Agent
有时候，你发送的请求，可能只有在浏览器下才能正常返回，而其他方式却不行。这可能跟请求头中的User-Agent有关。通过URLConnection发送的请求，默认会带上的User-Agent信息是Java/1.6.0_19,也就是java+jre的版本。你可以重写这个信息：
```java
connection.setRequestProperty("User-Agent", "Mozilla/5.0 (Windows; U; Windows NT 5.1; en-US; rv:1.9.2.3) Gecko/20100401"); // Do as if you're using Firefox 3.6.3.
```
这里有一份更全的浏览器[User-Agent清单](http://www.useragentstring.com/pages/useragentstring.php)

###错误处理
如果HTTP的响应码是4xx(客户端异常）或者5xx(服务端异常），你可以通过HttpURLConnection#getErrorStream()获取信息，服务端可能会将一些有用的错误信息放到这里面。
```java
InputStream error = ((HttpURLConnection) connection).getErrorStream();
```

###上传文件
一般来说，你需要将post的内容设为[multipart/form-data](http://www.w3.org/TR/html401/interact/forms.html#h-17.13.4.2)(相关的RFC文档：[RFC2388](http://www.faqs.org/rfcs/rfc2388.html))
```java
String param = "value";
File textFile = new File("/path/to/file.txt");
File binaryFile = new File("/path/to/file.bin");
String boundary = Long.toHexString(System.currentTimeMillis()); // Just generate some unique random value.
String CRLF = "\r\n"; // Line separator required by multipart/form-data.
URLConnection connection = new URL(url).openConnection();
connection.setDoOutput(true);
connection.setRequestProperty("Content-Type", "multipart/form-data; boundary=" + boundary);

try (
    OutputStream output = connection.getOutputStream();
    PrintWriter writer = new PrintWriter(new OutputStreamWriter(output, charset), true);
) {
    // Send normal param.
    writer.append("--" + boundary).append(CRLF);
    writer.append("Content-Disposition: form-data; name=\"param\"").append(CRLF);
    writer.append("Content-Type: text/plain; charset=" + charset).append(CRLF);
    writer.append(CRLF).append(param).append(CRLF).flush();

    // Send text file.
    writer.append("--" + boundary).append(CRLF);
    writer.append("Content-Disposition: form-data; name=\"textFile\"; filename=\"" + textFile.getName() + "\"").append(CRLF);
    writer.append("Content-Type: text/plain; charset=" + charset).append(CRLF); // Text file itself must be saved in this charset!
    writer.append(CRLF).flush();
    Files.copy(textFile.toPath(), output);
    output.flush(); // Important before continuing with writer!
    writer.append(CRLF).flush(); // CRLF is important! It indicates end of boundary.

    // Send binary file.
    writer.append("--" + boundary).append(CRLF);
    writer.append("Content-Disposition: form-data; name=\"binaryFile\"; filename=\"" + binaryFile.getName() + "\"").append(CRLF);
    writer.append("Content-Type: " + URLConnection.guessContentTypeFromName(binaryFile.getName())).append(CRLF);
    writer.append("Content-Transfer-Encoding: binary").append(CRLF);
    writer.append(CRLF).flush();
    Files.copy(binaryFile.toPath(), output);
    output.flush(); // Important before continuing with writer!
    writer.append(CRLF).flush(); // CRLF is important! It indicates end of boundary.

    // End of multipart/form-data.
    writer.append("--" + boundary + "--").append(CRLF).flush();
}
```

假设服务端还是一个[HttpServlet](http://docs.oracle.com/javaee/6/api/javax/servlet/http/HttpServlet.html),它的doPost()方法将会处理这个请求，服务端通过[HttpServletRequest#getPart()](http://docs.oracle.com/javaee/6/api/javax/servlet/http/HttpServletRequest.html#getPart%28java.lang.String%29)获取你发送的内容（注意了，不是getParameter()）。getPart()是个比较新的方法，是在Servlet 3.0后才引入的。如果你是Servlet 3.0之前的版本，则可以选用[Apache Commons FileUpload](http://commons.apache.org/fileupload]来解析multipart/form-data的请求。可以参考这里的[例子](http://stackoverflow.com/questions/2422468/upload-big-file-to-servlet/2424824#2424824)

###最后的话
上面啰嗦了很多，Apache提供了工具包，帮助我们更方便地完成这些事情
[Apache HttpComponents HttpClient](http://stackoverflow.com/questions/2422468/upload-big-file-to-servlet/2424824#2424824)：
- [HttpClient Tutorial](http://hc.apache.org/httpcomponents-client-ga/tutorial/html/)
- [HttpClient Examples](http://hc.apache.org/httpcomponents-client-ga/examples.html)


google也有类似的[工具包](https://code.google.com/p/google-http-java-client/)

解析、提取HTML内容
如果你是想解析提取html的内容，你可以用[Jsoup](http://jsoup.org/)等解析器
- [一些比较有名的HTML解析器的优缺点](http://stackoverflow.com/questions/3152138/what-are-the-pros-and-cons-of-the-leading-java-html-parsers/3154281#3154281)
- [用java如何扫描和解析网页](http://stackoverflow.com/questions/2835505/how-to-scan-a-website-or-page-for-info-and-bring-it-into-my-program/2835555#2835555)




stackoverflow原址：
http://stackoverflow.com/questions/2793150/using-java-net-urlconnection-to-fire-and-handle-http-requests
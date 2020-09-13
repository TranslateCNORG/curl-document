# curl教程

## 最普通的用法

获取百度的主页:

```bash
curl http://www.baidu.com/
```

获取ftp服务器funet的README文件:

```bash
curl ftp://ftp.funet.fi/README
```

从8000端口获取网页:

```bash
curl http://www.xxx.com:8000/ # www.xxx.com 纯属虚构
```

获取ftp服务器上的主页:

```bash
curl ftp://cool.haxx.se/
```

从dict服务器上中查找curl的资源(服务器dict.org在国外，访问缓慢):

```bash
curl dict://dict.org/m:curl
```

一次获取两个网页的主页，可以是指定端口的，也可以是ftp或者其他协议的网址:

```bash
curl http://www.baidu.com/ https://opentuna.cn/ # opentuna.cn必须使用https访问
```

从ftps服务器上获取文件:

```bash
curl ftps://files.are.secure.com/secrets.txt
```

或者使用--ftp-ssl参数和ftp网址获取文件:

```bash
curl --ftp-ssl ftp://files.are.secure.com/secrets.txt
```

通过ssh获取sftp服务器上的文件(`-u` 参数后要写登录sftp服务器的用户名(也可以直接在用户名后面加密码但要用 `:` 分割，如 admin@123456789)，如果连接服务器成功，会让你输入密码):

```bash
curl -u username sftp://example.com/etc/issue # example.com 纯属虚构，只是个事例
```

使用ssh从scp服务器上获取文件(在本地生成私钥，使用 `--key` 参数引入私钥在计算机上的位置。简要概括就是私钥免去了密码验证，如果密钥有密码请看下一个，这个也是需要看的)

```bash
curl -u username: --key ~/.ssh/id_rsa scp://example.com/~/file.txt
```

通过ssh从scp服务器上获取文件(--pass参数后面是密钥的密码)

```bash
curl -u username: --key ~/.ssh/id_rsa --pass private_key_password scp://example.com/~/file.txt
```

通过IPv6的ip获取服务器主页，和IPv4的ip获取服务器主页的区别是IPv6的ip被包裹在\[\]里:

```bash
# IPv6
curl http://[2001:1890:1112:1::20]/

# IPv4
curl http://223.5.5.5/
```

获取smb服务器的文件。`-u` 参数后的 `domain` 是域名，`username` 是登录服务器的用户名，`passwd` 是登录服务器的密码:

```bash
curl -u "domain\username:passwd" smb://server.example.com/share/file.txt
```

上面所有都是curl的最单的操作————获取网页/文件

## 下载文件

### 原理

curl先获取一个网页/文件，然后存储在本地一个文件，就做到了下载。

curl还有下载的功能，它能完全代替 `wget`。curl能下载任何类型的数据(如mp3,mp4,m4a,aac,html,css,js,cpp...)

获取网页然后存储为特定文件名，-o参数必须要特定文件名:

```bash
curl -o baidu.html https://www.baidu.com/
```

![下载时出现的界面](https://s1.ax1x.com/2020/09/11/wUkNWT.png)

下载时会出现上面的界面，表示curl正在获取网页，最后会保存为 `baidu.com` 文件。

我们先分割上面命令的参数(-o算第一个参数，以此类推):

|参数位置|值|解释|
|:-:|:-:|:-:|
|1|-o|下载一个网页/文件并要指定下载后保存的名字(有更简便的参数，在下面)|
|2|baidu.html|可自定义，网页/文件下载后保存的名字|
|3|https://www.baidu.com/|要下载的网页/文件|

和上面的功能一样，-O参数比-o参数更加方便，不用指定下载后的文件名，但是要在网址中指定文件名，说到这里你可能搞不懂了，什么才叫做没有指定文件名？这里的没有指定文件名的意思就是在网址中没有指定要下载的网页/文件的类型，就像原本要下载一张类型为png的图片 `https://xxx.com/xxx.png` ，但被你省略了后缀(.png `https://xxx.com/xxx`)，这就叫做网址中没有指定文件名，也可以叫做网址中没有指定下载网页/文件的后缀。你肯定会想到一个网页的主页该怎么下载呢？难道只能用 `-o` 了吗？`-O` 参数也能下载，你访问每个网页的主页都相当于访问 https://一个网址/index.html (index.html还可以是index.htm)，只要下载 `https://一个网址/index.html` 就能下载网页的主页。

```bash
curl -O https://www.baidu.com/index.html
```

## 给网页传输密码

curl能传输密码，既然能传输密码就能登录网页。

### Ftp

使用用户名密码登录ftp服务器，获取文件:

```bash
curl ftp://name:passwd@machine.domain:port/full/path/to/file
```

格式

```bash
curl ftp://<用户名>:<密码>@xxx.com:port/xxx
```

在 `<用户名>:<密码>` 后，有一个 `@` ，你注意到了吗？这种登录方式不仅能在ftp协议网址中用，还能在其他协议中用。

或者使用 `-u` 参数登录，:passwd可以省略，只用有用户名就好了，如果连接上了服务器会让你输入密码:

```bash
curl -u name:passwd ftp://machine.domain:port/full/path/to/file
```

### FTPS

它和ftp相似，如果你看了前面的ftps文件获取实例，你肯定发现了它需要一个 `--ftp-ssl` 参数。

请注意!建议不要不加 `--ftp-ssl` 参数获取文件，或者直接使用ftp(ftp://)协议

### SFTP / SCP

这两个和FTP相似，但是你可以使用 `--key` 参数来指定要使用的私钥的位置，而不是登录的密码密码。请注意，私钥可能受有密码保护，需要输入密码;该密码是使用 `--pass` 参数指定的。通常，curl将自动从私钥文件的目录下提取公钥，但是curl在没有适当的库支持的情况下，必须使用 `--pubkey` 选项指定的公钥文件的位置。

### HTTP

curl还支持HTTP网址的登录操作:

```bash
curl http://name:passwd@machine.domain/full/path/to/file
```

和ftp一样，还有一种:

```bash
curl -u name:passwd http://machine.domain/full/path/to/file
```

----

HTTP提供了许多不同的身份验证方法，curl支持这些身份验证方法:Basic、Digest、NTLM、Negotiate(SPNEGO)。你可能不知道使用哪种方法，所以curl默认为Basic。您还可以使用 `--anyauth` 参数让curl从服务器可使用身份验证方法中选择最安全的验证方法。

注意!根据URL规范，HTTP网址不能包含用户和密码，当通过网络代理使用curl时，这种方法不会起作用，尽管在其他时候curl允许这样做。使用网络代理时，用户和密码必须使用 `-u` 参数。

### HTTPS

常用于私有证书。

## Proxy

curl支持HTTP和SOCKS代理服务器，并提供多个的身份验证方法。它没有对FTP代理服务器支持，这个服务器目前没有标准，但是它仍然可以和其它许多服务器一起工作。您还可以使用HTTP和SOCKS代理在FTP服务器之间传输文件。

使用名为my-proxy的HTTP代理获取ftp文件，代理的端口是888(使用 `-x` 参数指定代理服务器):

```bash
curl -x my-proxy:888 ftp://ftp.leachsite.com/README
```

使用代理登录网页:

```bash
curl -u user:passwd -x my-proxy:888 http://www.get.this/
```

有些代理需要用户名和密码，用 `-U`(`--proxy-user`) 参数:

```bash
curl -U user:passwd -x my-proxy:888 http://www.get.this/
```

使用 `--noproxy` 参数来指定不走代理的网站:

```bash
curl --noproxy localhost,get.this -x my-proxy:888 http://www.get.this/
```

如果使用 `--proxy1.0` 参数而不是 `--proxy` 或 `-x` 参数指定代理，那么curl会使用 `HTTP/1.0` 而不是 `HTTP/1.1` 尝试连接服务器。

---

curl还支持SOCKS4和SOCKS5代理，使用它们的参数分别是 `--socks4` 和 `socks5`。

另外请参见curl支持的环境变量，环境这些变量提供了更好的代理使用。

站在客户端的角度来看，大多数FTP代理服务器都被设置成普通的FTP服务器，使用特殊的参数来选择远程FTP服务器。curl支持 `-u` 、`-Q` 和 `--ftp-account` 参数，这些参数可用于更多ftp代理进行的传输。例如，可以使用Blue Coat FTP代理将文件上载到远程FTP服务器:

```bash
curl -u "username@ftp.server Proxy-Username:Remote-Pass" \
--ftp-account Proxy-Password --upload-file local-file \
ftp://my-ftp.proxy.server:21/remote/upload/path/
```

* `ftp.server` 是ftp代理服务器的地址
* `Proxy-Username` 是连接代理时的用户名
* `Remote-Pass` 无需改动
* `Proxy-Password` 连接代理的密码
* `lical-file` 要上传的文件位置

请参阅FTP代理手册(未翻译)以确定设置它传输的形式，curl的 `-v` 参数可以查看curl和服务端交互的过程。

```bash
curl -v https://www.baidu.com/
```

## 获取范围

难道curl只能获取完整的网页/文件吗？不能指定范围？

curl是可以指定范围的，`-r` 参数用来指定范围。

HTTP1.1引入了byte(相当于一个字)范围。使用此方法，客户端可以请求仅获取指定网页/文件的一部分。curl用 `-r` 参数支持这一点。

获取网页/文件前100个字，第一个算0，以此类推:

```bash
curl -r 0-99 http://www.baidu.com/
```

获取网页/文件从最后一个字开始算，获取500个字:

```bash
curl -r -500 https://m.baidu.com/ # 不用 www.baidu.com 的原因是字太少了
```

Curl还支持指定FTP服务器上的文件的范围。只能指定开始位置和停止位置。

获取ftp服务器上的文件的前100个字:

```bash
curl -r 0-99 ftp://www.get.this/README
```

## 上传文件

### FTP / FTPS / SFTP / SCP

将标准输出的所有数据上传到指定的服务器，使用 `-T` 参数把数据上传到指定的服务器，仅对上传数据无需密码和用户名的服务器生效:

*标准输入，一般指键盘输入到缓冲区里的东西。*

```bash
curl -T - ftp://ftp.upload.com/myfile
```

使用用户名和密码登录服务器，上传数据，`uploadfile` 是要上传的文件的位置:

```bash
curl -T uploadfile -u user:passwd ftp://ftp.upload.com/myfile
```

将本地文件上传到服务器，并使用服务器上的文件名:

```bash
curl -T uploadfile -u user:passwd ftp://ftp.upload.com/xx.xxx
```

上传本地文件附加到目标文件，`-a` 参数的意思是上传时附加到目标文件:

```bash
curl -T localfile -a ftp://ftp.upload.com/remotefile
```

curl还支持使用代理进行ftp上传，但前提是代理被配置允许这种隧道传输。如果有配置，就可以使用类似于以下方式，`--proxytunnel` 参数是代理已配置隧道的意思:

```bash
curl --proxytunnel -x proxy:port -T localfile ftp.upload.com
```

### SMB / SMBS

和ftp差不多:

```bash
curl -T file.txt -u "domain\username:passwd" \
  smb://server.example.com/share/
```

### HTTP

将标准输出的所有数据上传到指定的HTTP网站:

```bash
curl -T - http://www.upload.com/myfile
```

请注意!必须先将HTTP服务器配置设置为接受PUT请求，才能成功执行此操作。

有关HTTP数据上传的其他方法，请看下面的POST部分(不是下一个)。

## Verbose / Debug

curl在某些地方出错、服务器不允许您进入、您无法理解响应，用 `-v` 参数获取详细的curl和服务端交互过程。`-v` 参数会输出大量信息以及它发送和接收的内容，以便让用户看到所有的客户端和服务器交互(但它不会向您显示实际的数据)。

```bash
curl -v http://www.baidu.com/
```

要获得更多curl和服务端交互的细节和信息，请使用 `--trace` 或 `--trace-ascii` 参数，把完整交互过程保存成文件:

```bash
curl --trace baidu.txt https://www.baidu.com/
```

## 详细资料

不同的协议提供了获取网页/文件的头部信息的不同方法。要让curl显示一个文件的头部信息，要使用 `-I`/`--head` 参数。它可以显示一个HTTP或FTP网页/文件的头部信息。HTTP头部信息要广泛得多。

*网页头部信息和网页标头意思一样*

```bash
curl -I https://www.baidu.com/
```

*头部信息是指，服务端返回过来的响应信息，由于它显示在返回的数据最前面，所以叫做头部信息*

对于HTTP，您可以使用 `-I`/`--include` 获取显示在数据前面的头信息（与 `-I` 相同）。curl还可以保存FTP和HTTP网页/文件的头部信息在指定的文件，要使用 `-D`/`--dump` 参数。

```bash
curl -D baiduheaders.txt https://www.baidu.com/ # 把 www.baidu.com 的头部信息保存在 baiduheaders.txt 文件中
```

## POST传输(HTTP)

使用curl很容易传输数据。这是使用 `-d <data>` 参数完成的。传输数据必须是编码过的。使用了 `-d` 参数，curl会给 HTTP 标头加上 `Content-Type : application/x-www-form-urlencoded`

```bash
curl -d "name=Rafael%20Sagula&phone=3320780" http://www.where.com/guest.cgi
```

*空格编码后是 %20*

使用 POST 协议给网站传输 name 值和 phone 值。

*POST 是 HTTP 协议中的一种，用于给服务端传输数据，和 GIT 最大的区别是 GET 是在链接上做请求，如 https://www.where.com/guest.cgi?name=Rafael%20Sagu&phone=3320780*

##### 如何发送表单数据

找出网页表单中所有要填写的 `<input>` 标签。

*<input> 标签是 HTML 中让用户输入数据的标签*

如果有一个“正常”的 POST 请求，你可以用 `-d` 来传输数据。`-d` 接受完整的 “post string”，格式为:

```
<variable1>=<data1>&<variable2>=<data2>&...
```

*post string 就是把一段字符串组织出来的 POST 请求*

`variable*` 名称是在 `<input>` 标签中的 `name` 属性的值，就是请求的名称，而 `data*` 是您要为输入的内容。数据必须经过的URL编码。这意味着要用 `%XX` 替换奇怪的字母(如中文，中文符号，空格)，其中 `XX` 是字母的ASCII码的十六进制表示。

*\* 在linux中表示多个任意字*

事例:

(网页在 http://www.formpost.com/getthis/)

```html
<form action="post.cgi" method="post">
<input name=user size=10>
<input name=pass type=password size=10>
<input name=id type=hidden value="blablabla">
<input name=ding value="submit">
</form>
```

我们要输入的用户名是 `foobar` ，密码是 `12345`。

```bash
curl -d "user=foobar&pass=12345&id=blablabla&ding=submit" \
  http://www.formpost.com/getthis/post.cgi
```

`-d` 参数使用的是 `application/x-www-form-urlencoded` 传输类型，CGI和类似的文件通常都能理解，但是curl也支持功能更强的 `multipart/form` 传输类型，这一种类型很像文件上传。

`-F` 参数的值是 `-F "name=contents"` 这样的。如果要从文件中读取内容再传输，请使用 `@filename` 作为文件。指定文件时，还可以通过在文件名后追加 `;type=<mime type>` 来指定文件内容类型。也可以在一个字段中发布多个文件的内容，curl会自动给 HTTP 标头添加 `Content-Type: multipart/form-data` 。例如，属性名 `coolfiles` 用于发送三个不同类型的文件，使用以下语法:

```bash
curl -F "coolfiles=@fil1.gif;type=image/gif,fil2.txt,fil3.html" \
  http://www.post.com/postit.cgi
```

如果没有指定内容类型，curl将尝试从文件后缀推测(curl只知道几个文件后缀)，或者使用以前指定的类型(如果指定了多个文件，会使用以前指定过的类型的文件)，如果文件后缀是以前没有指定过的文件类型，它将使用默认类型 `application/octet-stream` 。

还可以用 `-F` 模拟填充表单。假设您在一个表单中填充了三个字段。一个字段是要发布的文件名，一个字段是您的名字，一个字段是文件说明。我们要发布我们写的文件 `cooltext.txt` 。 为了让curl代替您经常使用的浏览器传输这些数据，您必须看表单页面的HTML源码并找到 `input` 标签的name属性。在我们的示例中，`input name` 为`file` 、`yourname` 和 `filedescription` 。

```bash
curl -F "file=@cooltext.txt" -F "yourname=Daniel" \
  -F "filedescription=Cool text file with cool text inside" \
    http://www.post.com/postit.cgi
```

如果你要一次发送两个及以上的文件，有两种办法给你选择:

在一个 name 里填写多个文件，用 `,` 分隔:

```bash
curl -F "pictures=@dog.gif,cat.gif" $URL
```

填写两个及以上的 name(每个前都要有 `-F` 参数):

```bash
curl -F "docpicture=@dog.gif" -F "catpicture=@cat.gif" $URL
```

如果要按字面方式发送 name 值，而不解释前缀是 `@` 或 `<` ,或嵌入的 `;type=` ，请使用 `--form-string` 而不是 `-F` 。当值是从用户或其他不可预知的地方获取时，建议使用这种方法。在这种情况下(不是前面那种情况)，使用 `-F` 而不是 `--form-string` 用户可以欺骗curl上传文件。

还可以使用 `;filename=` 指定上传后的文件名。

## Referer

设置 HTTP 的标头 Referer，表示请求的来源(没啥用)。使用 `-e` 参数就能做到。

```bash
curl -e https://www.baidu.com https://www.csdn.net
```

## 用户标志(User Agent)

curl可以改变请求中的User Agent(简称ua)。使用 `-A` 参数指定ua。

```bash
curl -A ''Mozilla/3.0 (Win95; I)' https://www.baidu.com
```

其他常用的ua:

* `Mozilla/3.0 (Win95; I)` - Netscape Version 3 在  Windows 95
* `Mozilla/3.04 (Win95; U)` - Netscape Version 3 在 Windows 95
* `Mozilla/2.02 (OS/2; U)` - Netscape Version 2 在 OS/2
* `Mozilla/4.04 [en] (X11; U; AIX 4.2; Nav)` - Netscape 在 AIX
* `Mozilla/4.05 [en] (X11; U; Linux 2.0.32 i586)` - Netscape 在 Linux

请注意，Internet Explorer(简称IE)尽量在各个方面保持兼容(IE准备停止维护了，推荐不要用IE ua):

* `Mozilla/4.0 (compatible; MSIE 4.01; Windows 95)` - MSIE for W95

非Mozilla的ua:

* `Konqueror/1.0` - KDE文件管理器桌面客户端
* `Lynx/2.7.1 libwww-FM/2.14` - Lynx命令行浏览器

## Cookies

*Cookie，有时也用其复数形式 Cookies。类型为“小型文本文件”，是某些网站为了辨别用户身份，进行Session跟踪而储存在用户本地终端上的数据（通常经过加密），由用户客户端计算机暂时或永久保存的信息。*

cookies通常被web服务器用来在客户端保存信息。服务器通过在 HTTP 标头中发送一个响应来设置Cookie `Set-Cookie: <data>` ，其中 data 部分通常包含一组 `NAME=VALUE` 键值对(用分号分隔 `;` 如 `NAME1=VALUE1;NAME2=VALUE2;`;。服务器还可以指定 cookie 存在哪个路径(通过指定 `path=value`)、cookie 什么时候过期(`expire=DATE`)、哪个网页能使用cookie(`domain=NAME`)、以及是否只在https连接时使用(`secure`)。在curl中使用 `-b` 参数设置cookie。

*键值对由键和值组成一对，上面的NAME就是键，VALUE是值*

如果服务器在标头中返回了这样的cookie:

```
Set-Cookie: sessionid=boo123; path="/foo";
```

意思是在路径为 `/foo` 的位置存储 `boo123`。

例如，在一个网页发送cookie，存入我的名字:

```bash
curl -b "name=lihai2333" www.sillypage.com
```

`--dump-header` 用于把网页头部信息保存成指定文件:

```bash
curl --dump-header headers www.baidu.com
```

执行完命令，会发现目录下多了一个headers文件，打开能看到最后一行的 `Set-Cookie: BDORZ=27315; max-age=86400; domain=.baidu.com; path=/` 。

然后可以把保存下来的 `headers` 再发送到网页。可以是同一个网页，也可以是另一个网页:

```bash
curl -b headers m.baidu.com
```

使用标头设置cookie容易出错，这也不是一种很普遍的方法，curl提供了一个叫做 `-c` 的参数，它可以保存服务器设置的cookie:

```bash
curl -c cookie.txt www.baidu.com
```

请注意，`-b` + `-L`(如果网页要跳转至新网页会自动跳转)可以把cookie新页面(如果有)。发送给服务器的cookie不一定有，例如:

```bash
curl -L -b a.txt https://www.baidu.com # a.txt 是不存在的
```

读取的cookie文件必须使用纯HTTP标头或netscape的cookie文件格式化过。curl根据文件内容确定它是哪种类型。在上面的命令中，curl将解析后的标头并发送到 https://www.baidu.com 。cookie是可以没有的。

还可以从服务器获取cookie然后再发送给服务器:

```bash
curl -b cookie.txt -c cookie.txt https://www.baidu.com
```

## 进度表

当你下载网页/文件时应该会出现这样的表:

![下载时出现的界面](https://s1.ax1x.com/2020/09/11/wUkNWT.png)

进度条是为了显示实时更新的一些东西，就像下载进度。

含义解释(从左到右):

* % - 完成工作的百分比
* Total - 整个网页/文件总大小
* % - 已经下载的百分比
* Received - 已经下载的大小
* % - 上传完成的百分比
* Xferd - 已经上传的百分比
* Average Speed Dload - 下载的平均速度
* Average Speed Upload  - 上传的平均速度
* Time Total - 完成工作的总时间
* Time Spent - 当前已用用的时间
* Time Left - 预计完成时间
* Current Speed - 最后5秒的平均传输速度

参数 `-#` 表示一个完全不同的百分进度条，只有下载进度。



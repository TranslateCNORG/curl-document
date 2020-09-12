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

和上面的功能一样，-O参数比-o参数更加方便，不用指定下载后的文件明，但是要在网址中指定文件名，说到这里你可能搞不懂了，什么才叫做没有指定文件名？这里的没有指定文件名的意思就是在网址中没有指定要下载的网页/文件的类型，就像原本要下载一张类型为png的图片 `https://xxx.com/xxx.png` ，但被你省略了后缀(.png `https://xxx.com/xxx`)，这就叫做网址中没有指定文件名，也可以叫做网址中没有指定下载网页/文件的后缀。你肯定会想到一个网页的主页该怎么下载呢？难道只能用 `-o` 了吗？`-O` 参数也能下载，你访问每个网页的主页都相当于访问 https://一个网址/index.html (index.html还可以是index.htm)，只要下载 `https://一个网址/index.html` 就能下载网页的主页。

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

最常用于私有证书。

### Proxy

curl支持HTTP和SOCKS代理服务器，并提供多个的身份验证方法。它没有对FTP代理服务器支持，这个服务器目前没有标准，但是它仍然可以和其它许多服务器一起工作。您还可以使用HTTP和SOCKS代理在FTP服务器之间传输文件。

使用名为my-proxy的HTTP代理获取ftp文件，代理的端口是888(使用 `-x` 参数指定代理服务器):

```bash
curl -x my-proxy:888 ftp://ftp.leachsite.com/README
```

使用代理登录网页:

```bash
curl -u user:passwd -x my-proxy:888 http://www.get.this/
```



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

通过ssh获取sftp服务器上的文件(`-u` 参数后要写登录sftp服务器的用户名，如果连接服务器成功，会让你输入密码):

```bash
curl -u username sftp://example.com/etc/issue # example.com 纯属虚构，只是个事例
```

使用ssh从scp服务器上获取文件(在本地生成密钥，使用--key参数引入密钥在计算机上的位置。简要概括就是密钥免去了密码验证，如果密钥有密码请看下一个，这个也是需要看的)

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

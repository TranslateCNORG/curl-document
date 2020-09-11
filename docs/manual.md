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

通过ssh获取sftp服务器上的文件:

```bash
curl -u username sftp://example.com/etc/issue # example.com 纯属虚构，只是个事例
```

使用ssh从scp服务器上获取文件(在本地生成密钥，使用--key参数引入密钥在计算机上的位置)

```bash
curl -u username: --key ~/.ssh/id_rsa scp://example.com/~/file.txt
```


### 不带有任何参数时，curl 就是发出 GET 请求。
---

```bash
$ curl https://www.example.com
 ```


上面命令向`www.example.com`发出 GET 请求，服务器返回的内容会在命令行输出。

### -A
---
`-A`参数指定客户端的用户代理标头，即`User-Agent`。curl 的默认用户代理字符串是`curl/[version]`

```bash
$ curl -A 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/76.0.3809.100 Safari/537.36' https://google.com
```

### -b
---
`-b`参数用来向服务器发送 Cookie。

 ```bash 
 $ curl -b 'foo=bar' https://google.com
 ```

上面命令会生成一个标头`Cookie: foo=bar`，向服务器发送一个名为`foo`、值为`bar`的 Cookie。

```bash
 $ curl -b 'foo1=bar;foo2=bar2' https://google.com
 ```

上面命令发送两个 Cookie。

```bash 
 $ curl -b cookies.txt https://www.google.com
 ```

上面命令读取本地文件`cookies.txt`，里面是服务器设置的 Cookie（参见`-c`参数），将其发送到服务器。

### -c
---
`-c`参数将服务器设置的 Cookie 写入一个文件。

```bash
$ curl -c cookies.txt https://www.google.com
```

### -d
---
`-d`参数用于发送 POST 请求的数据体

```bash
$ curl -d'login=emma＆password=123'-X POST https://google.com/login
# 或者
$ curl -d 'login=emma' -d 'password=123' -X POST  https://google.com/login
```

```bash
$ curl -d '@data.txt' https://google.com/login
```

### --data-urlencode
---
`--data-urlencode`参数等同于`-d`，发送 POST 请求的数据体，区别在于会自动将发送的数据进行 URL 编码。

```bash
$ curl --data-urlencode 'comment=hello world' https://google.com/login
```

### -e
---
`-e`参数用来设置 HTTP 的标头`Referer`，表示请求的来源。

```bash
curl -e 'https://google.com?q=example' https://www.example.com
```

### -F
---
`-F`参数用来向服务器上传二进制文件。
```bash
$ curl -F 'file=@photo.png' https://google.com/profile
```

上面命令会给 HTTP 请求加上标头`Content-Type: multipart/form-data`，然后将文件`photo.png`作为`file`字段上传。

`-F`参数可以指定 MIME 类型，文件名。

```bash
$ curl -F 'file=@photo.png;type=image/png' https://google.com/profile

$ curl -F 'file=@photo.png;filename=me.png' https://google.com/profile
```

### -G
---
`-G`参数用来构造 URL 的查询字符串。
```bash
$ curl -G -d 'q=kitties' -d 'count=20' https://google.com/search
```

上面命令会发出一个 GET 请求，实际请求的 URL 为`https://google.com/search?q=kitties&count=20`。如果省略`--G`，会发出一个 POST 请求

### -H
---
`-H`参数添加 HTTP 请求的标头
```bash
$ curl -H 'Accept-Language: en-US' https://google.com
```

```bash
$ curl -d '{"login": "emma", "pass": "123"}' -H 'Content-Type: application/json' https://google.com/login
```

### -i
---
`-i`参数打印出服务器回应的 HTTP 标头。
```bash
$ curl -i https://www.example.com
```

### -I
---
`-I`参数向服务器发出 HEAD 请求，然后会将服务器返回的 HTTP 标头打印出来。
```bash
$ curl -I https://www.example.com
```

### -k
---
`-k`参数指定跳过 SSL 检测。
```bash
$ curl -k https://www.example.com
```

### -L
---
`-L`参数会让 HTTP 请求跟随服务器的重定向。curl 默认不跟随重定向。
```bash
$ curl -L -d 'tweet=hi' https://api.twitter.com/tweet
```

### --limit-rate
---
`--limit-rate`用来限制 HTTP 请求和回应的带宽，模拟慢网速的环境。

```bash
$ curl --limit-rate 200k https://google.com
```

### -o
---
`-o`参数将服务器的回应保存成文件，等同于`wget`命令

```bash
$ curl -o example.html https://www.example.co
```

### -O
---
`-O`参数将服务器回应保存成文件，并将 URL 的最后部分当作文件名。

```bash
$ curl -O https://www.example.com/foo/bar.html
```

### -s
---
`-s`参数将不输出错误和进度信息。
```bash
$ curl -s https://www.example.co
```

```bash
$ curl -s -o /dev/null https://google.com
```

### -S
---
`-S`参数指定只输出错误信息，通常与`-s`一起使用

### -u
---
`-u`参数用来设置服务器认证的用户名和密码。

```bash
$ curl -u 'bob:12345' https://google.com/login
```

上面命令设置用户名为`bob`，密码为`12345`，然后将其转为 HTTP 标头`Authorization: Basic Ym9iOjEyMzQ1`。

curl 能够识别 URL 里面的用户名和密码。

### -v
---
`-v`参数输出通信的整个过程，用于调试。

```bash
$ curl -v https://www.example.com
```

### -x
---
`-x`参数指定 HTTP 请求的代理。

```bash
$ curl -x socks5://james:cats@myproxy.com:8080 https://www.example.com
```

### -X
---
`-X`参数指定 HTTP 请求的方法。

```bash
$ curl -X POST https://www.example.com
```

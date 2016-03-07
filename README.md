learn-curl-in-chinese
=====================

email:kutekute00@gmail.com

=====================

##CURL install instruction

###在unix下安装

首先你需要下载source，解压进入之后

	./configure
	make
	make test (optional)
	make install

可能你得以root权限执行，安装完成后，如果你想获得可用的configure options，这样：

    ./configure --help

如果你不想安装在/usr/local中，这样：

    ./configure --prefix=/path/to/curl/tree

如果你恰巧对某一目录有*写*的权限，你可以不用root，下面的例子是安装在你的home目录

    ./configure --prefix=$HOME
    make
    make install

默认configure script会去查找SSL,如果你安装的OpenSSL在默认的serach path，那你无需配置；如果你已经安装SSL在/usr/local/ssl中，那么你应该这样运行配置：

    ./configure --with-ssl

如果你安装OpenSSL在其他地方（比如/opt/OpenSSL),而且你也安装了pkg-config，首先要设置pkg-config

    env PKG_CONFIG_PATH=/opt/OpenSSL/lib/pkgconfig ./configure --with-ssl

如果你没安装pkg-config，那么

    ./configure --with-ssl=/opt/OpenSSL


=====================


##这里是关于curl的讲解的一些例子

从Netscape's的web-server上获取main page内容

	curl http://www.netscape.com/

从kute的ftp服务器上获取README文件

	curl ftp://ftp.funet.fi/README

从服务器上获取端口为8000的页面
 
        curl http://www.weirdserver.com:8000/
 
从FPT站点获取获取列表
 
        curl ftp://cool.haxx.se/
 
从字典中获取`curl`的定义
 
        curl dict://dict.org/m:curl
 
一次获取两个页面
 
        curl ftp://cool.haxx.se/ http://www.weirdserver.com:8000/

从FTPS上下载文件
 
        curl ftps://files.are.secure.com/secrets.txt
 
或者用符合FPTS的方式下载
 
        curl --ftp-ssl ftp://files.are.secure.com/secrets.txt
 
使用SFTP协议从SSH服务器上获取文件
 
        curl -u username sftp://shell.example.com/etc/issue
 
使用SCP协议通过私钥从SSH server上获取文件
 
        curl -u username: --key ~/.ssh/id_dsa --pubkey ~/.ssh/id_dsa.pub \ scp://shell.example.com/~/personal.txt
 
从IPv6 web server上获取文件
 
        curl "http://[2001:1890:1112:1::20]/"

##下载文件

自定义保存获取到的页面

	curl -o thatpage.html http://www.netscape.com/

获取一个页面，保存的文件名称使用原来的文件名（如果原来的页面没有file name，此命令会挂了）

	curl -O http://www.netscape.com/index.html

同理，同时获取两个文件，分别都使用他们原来的文件名

	curl -O www.haxx.se/index.html -O curl.haxx.se/download.html

##使用验证密码

###针对`FTP`

获取fpt上的文件，并且使用username和passwd包含在url中的方式

	curl ftp://name:passwd@machine.domain:port/full/path/to/file

或者使用参数

	curl -u name:passwd ftp://machine.domain:port/full/path/to/file

###针对`FTPS`

FTPS和FTP差不多，推荐使用`FTP://`和`--ftp-ssl`参数结合的方式

###针对`HTTP`

	curl http://name:passwd@machine.domain/full/path/to/file

或者

	curl -u name:passwd http://machine.domain/full/path/to/file

###针对`HTTPS`

一般使用证书来获取HTTPS，有如下这些方式

1. 代理

     curl支持HTTP和socks代理服务器，不支持FTP代理，因为还没什么统一的标准，但是仍然可以有效地使用

	使用名为`my-proxy`，端口为`888`的HTTP代理来获取一个ftp file

		curl -x my-proxy:888 ftp://ftp.leachsite.com/README
	使用和上面一样的代理，不同的是这次需要user和passwd

		curl -u user:passwd -x my-proxy:888 http://www.get.this/

	有一些代理需要声明认证，通过`-U`参数

		curl -U user:passwd -x my-proxy:888 http://www.get.this/

2. 范围

	HTTP 1.1版本引入了`byte-ranges`，由此可以获取其中的一部分或者多个部分的内容

	获取一个页面的前100个字节

		curl -r 0-99 http://www.get.this/

	获取最后500个字节

		curl -r -500 http://www.get.this/

	也支持获取某个范围的，你只需要指明start和end范围，这个特性也是支持ftp的，例如获取ftp上文件的前100个byte

		curl -r 0-99 ftp://www.get.this/README

##上传文件

*使用`FTP`, `FTPS`, `SFTP`, `SCP`协议，这些是一样的用法*

把从标准输入（`stdin`)的数据上传到指定server

	curl -T - ftp://ftp.upload.com/myfile

上传指定文件的数据，使用`user`和`passwd`的方式

	curl -T uploadfile -u user:passwd ftp://ftp.upload.com/myfile

上传本地文件到远程站点，并用本地文件名来命名

	curl -T uploadfile -u user:passwd ftp://ftp.upload.com/

追加本地文件到远程站点文件

	curl -T localfile -a ftp://ftp.upload.com/remotefile

`curl`也支持通过代理的方式进行ftp上传，但是这种代理必须配置成允许`curl`这种隧道的方式

	curl --proxytunnel -x proxy:port -T localfile ftp.upload.com

*使用`HTTP`协议*

把从标准输入（`stdin`)的数据上传到指定的HTTP 站点

	curl -T - http://www.upload.com/myfile

请注意，这种方式必须是在你已经配置了HTTP接受`PUT`的方式的前提下才会有效，对于`HTTP`的其他方式，请看下面的`POST`章节

##VERBOSE / DEBUG

如果`curl`失败了，或者你吃了`server`的闭门羹don't let you in，或者你不理解`responses`，那就用`-v`标志来获取详细信息。`curl`会输出许多信息，包括它发送和接收的，你可以看到`client`和`server`的交互（*但是不会给你展示实际的数据*）

	curl -v ftp://ftp.upload.com/

为了能看到更多的调试信息，你可以使用`--trace`或者`--trace-ascii`参数，同时指定一下要记录这些信息的文件

	curl --trace trace.txt www.haxx.se

##关于详细信息

不同的协议提供了针对指定文件或者文档不同的获取详细信息的方式。为了获取单个文件的详细信息，你应该使用`-I`或者`--head`参数，它展示了针对HTTP和FTP上的单个文件的所有可用信息。

例如把HTTP的报头信息（`headers`)存储在一个单独的文件中

	curl --dump-header headers.txt curl.haxx.se

注意，如果你想获取`server`发送的`cookies`信息，那么把报头信息存储在单独的文件中是非常有用的

##POST(HTTP)

使用curl来post data是很容易的，你可以通过`-d`参数来实现(*注意 post的data必须是经过编码的*)

例如post `name`和`phone`参数

	curl -d "name=Rafael%20Sagula&phone=3320780" \ 
                 http://www.where.com/guest.cgi

那么怎么样体检一个表单（`form`）呢

首先你要找出表单中所有你想提交的`<input>`标签（在curl的官方站点中有个叫做`formfind.pl`的`perl`程序可以帮你完成这个任务）

如果现在有个提交的请求，你可以使用`-d`来提交，它使用如下的方式进行提交

	<variable1>=<data1>&<variable2>=<data2>&...

`variable`名就是`<input>`标签的`name`属性，`data`就是你填写的数据，*数据必须经过`URL encoded`*，也就是说你必须用`+`来替代空格，用`%XX`这样的形式来替代特殊字符

例如在[http://www.formpost.com/getthis/](http://www.formpost.com/getthis/ "http://www.formpost.com/getthis/")页面有如下表单

	<form action="post.cgi" method="post">
	<input name=user size=10>
	<input name=pass type=password size=10>
    <input name=id type=hidden value="blablabla">
    <input name=ding value="submit">
    </form>

我们填写`user`为`foobar`，`password`为`12345`，现在开始进行提交

	curl -d "user=foobar&pass=12345&id=blablabla&ding=submit"  (continues)
          http://www.formpost.com/getthis/post.cgi

`-d`参数使用的`mime-type`是`application/x-www-form-urlencoded`，curl也支持`multipart/form-data`，这种形式例如在文件上传

`-F`接受`-F "name=contents"`形式的参数，如果你的`contents`是从文件中读出的，那么使用`@filename`的形式，当然你可以通过追加`;type=<mime type>'`指明文件的`content type`。你也可以在同一个域中推荐多个文件的内容，例如下面有个名为`coolfiles`的域同时提交三个文件，这三个文件的`content type`是不同的

	curl -F "coolfiles=@fil1.gif;type=image/gif,fil2.txt,fil3.html" \ 
        http://www.post.com/postit.cgi

如果文件没有指明`content-type`，那么curl会从文件的扩展名来推测或者使用前面已经指明`content-type`的文件的类型，否则curl会使用默认的`application/octet-stream`类型

在一个`post`请求中同时发送两个文件有两种方式：

1. 在一个域中提交两个文件

		curl -F "pictures=@dog.gif,cat.gif"


2. 用两个域来提交

		 curl -F "docpicture=@dog.gif" -F "catpicture=@cat.gif"



*有点困了*

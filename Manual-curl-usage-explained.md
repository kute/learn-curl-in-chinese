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

如果`curl`失败了，或者你吃了`server`的闭门羹，或者你不理解`responses`，那就用`-v`标志来获取详细信息。`curl`会输出许多信息，包括它发送和接收的，你可以看到`client`和`server`的交互（*但是不会给你展示实际的数据*）

	curl -v ftp://ftp.upload.com/

为了能看到更多的调试信息，你可以使用`--trace`或者`--trace-ascii`参数，同时指定一下要记录这些信息的文件

	curl --trace trace.txt www.haxx.se


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
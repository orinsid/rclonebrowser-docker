# rclonebrowser-docker

在arm64平台上编译的rclonebrowser镜像

rclone=v1.66.0

rclonebrowser=1.8.0

alpine=3.15

[docker项目地址](https://hub.docker.com/r/orinsid/rclonebrowser)

感谢[romancin](https://github.com/romancin/rclonebrowser-docker)和[kapitainsky](https://github.com/kapitainsky/RcloneBrowser)为这个项目做出的努力，感谢[lesage](https://github.com/jlesage/docker-baseimage-gui)为 GUI 应用程序提供了出色的arm64基础映像。

其他平台和英文使用文档可以在[这里](https://github.com/romancin/rclonebrowser-docker)找到

### 运行示例：

![运行示例](https://github.com/orinsid/rclonebrowser-docker/blob/master/RcloneBrowser.jpg)

### 指示：

将任何本地端口映射到 5800 以进行 Web 访问

将任何本地端口映射到 5900 以进行 VNC 访问

将本地卷映射到 /config（存储配置数据）

将本地卷映射到 /media（访问媒体文件）

示例运行命令：
#### Docker
```bash
docker run -d --name=rclonebrowser \
-v /share/Container/rclonebrowser/config:/config \
-v /share/Container/rclonebrowser/media:/media \
-e GROUP_ID=0 -e USER_ID=0 -e TZ=Europe/Madrid \
-p 5800:5800 \
-p 5900:5900 \
orinsid/rclonebrowser:latest
```
#### Docker-Compose格式
```bash
version: "3.3"
services:
  rclonebrowser:
    container_name: rclonebrowser
    volumes:
      - /share/Container/rclonebrowser/config:/config
      - /share/Container/rclonebrowser/media:/media
    environment:
      - GROUP_ID=0
      - USER_ID=0
      - TZ=Europe/Madrid
    ports:
      - 5800:5800
      - 5900:5900
    image: orinsid/rclonebrowser:latest
networks: {}
```
浏览`http://your-host-ip:5800`以访问 RClone Browser GUI

### 环境变量
要自定义容器的某些属性，可以通过`-e` 参数传递以下环境变量（每个变量一个）。 该参数的值的格式为 `<VARIABLE_NAME>=<VALUE>`

| 参数       | 描述                                 | 默认值 |
|----------------|----------------------------------------------|---------|
|`USER_ID`| 应用程序运行的用户 ID。 请参阅[用户/组 ID](#用户组-id)以更好地了解何时应进行设置 | `1000` |
|`GROUP_ID`| 应用程序运行所在组的 ID。 请参阅[用户/组 ID](#用户组-id) 以更好地了解何时应设置此值| `1000` |
|`SUP_GROUP_IDS`| 应用程序的补充组 ID 的逗号分隔列表 | (unset) |
|`UMASK`| 控制如何为新创建的文件设置文件权限的掩码。 掩码的值采用八进制表示法。 默认情况下，未设置此变量，并使用默认的 umask“022”，这意味着新创建的文件可供所有人读取，但只能由所有者写入。 请参阅以下在线 umask 计算器：http://wintelguy.com/umask-calc.pl | (unset) |
|`TZ`| 容器的[时区]。 还可以通过在主机和容器之间映射“/etc/localtime”来设置时区 | `Etc/UTC` |
|`KEEP_APP_RUNNING`| 当设置为“1”时，如果应用程序崩溃或用户退出，应用程序将自动重新启动 | `0` |
|`APP_NICENESS`| 应用程序应运行的优先级。 尼斯值 -20 是最高优先级，19 是最低优先级。 默认情况下，未设置niceness，这意味着使用默认的niceness 0。 **注意**：负好感（优先级增加）需要额外的权限。 在这种情况下，容器应使用 docker 选项“--cap-add=SYS_NICE”运行| (unset) |
|`CLEAN_TMP_DIR`| 当设置为“1”时，“/tmp”目录中的所有文件都会在容器启动期间删除 | `1` |
|`DISPLAY_WIDTH`| 应用程序窗口的宽度（以像素为单位） | `1280` |
|`DISPLAY_HEIGHT`| 应用程序窗口的高度（以像素为单位） | `768` |
|`SECURE_CONNECTION`| 当设置为“1”时，将使用加密连接来访问应用程序的 GUI（通过 Web 浏览器或 VNC 客户端）。 有关更多详细信息，请参阅[安全](#安全) 部分 | `0` |
|`VNC_PASSWORD`| 连接到应用程序 GUI 所需的密码。 有关更多详细信息，请参阅 [VNC 密码](#vnc-密码) 部分| (unset) |
|`X11VNC_EXTRA_OPTS`| 传递到 Docker 容器中运行的 x11vnc 服务器的额外选项。 **警告**：对于高级用户。 除非您知道自己在做什么，否则请勿使用 | (unset) |
|`ENABLE_CJK_FONT`| 当设置为“1”时，将安装开源计算机字体“WenQuanYi Zen Hei”。 该字体包含大量中文/日文/韩文字符 | `0` |

### 数据存储

下表描述了容器使用的数据存储目录。 映射通过“-v”参数设置。 每个映射都用以下指定格式：

```<HOST_DIR>:<CONTAINER_DIR>[:PERMISSIONS]```

| 容器路径 | 权限 | 描述 |
|-----------------|-------------|-------------|
|`/config`| rw | 这是应用程序存储其配置、日志和任何需要持久性的文件的地方 |
|`/media`| rw | 这是存储下载的文件的位置，或者是您将文件放在主机中以供上传的位置 |

### 端口

这是容器使用的端口列表。 它们可以映射到主机通过“-p”参数（每个端口映射一个）。 每个映射都定义在以下格式：

```<HOST_PORT>:<CONTAINER_PORT>```

里面的端口号容器无法更改，但您可以自由使用主机端的任何端口。

| 端口 | 映射到主机 | 描述 |
|------|-----------------|-------------|
| 5800 | 必须 | 用于通过 Web 界面访问应用程序 GUI 的端口 |
| 5900 | 可选 | 用于通过 VNC 协议访问应用程序 GUI 的端口。 如果不使用 VNC 客户端，则可选 |

## 用户/组 ID

使用数据卷（“-v”标志）时，数据卷之间可能会出现权限问题主机和容器。 例如，容器内的用户可能不存在于主机上。 这可能会阻止主机正确访问文件以及共享卷上的文件夹。

为了避免出现任何问题，您可以指定应用程序运行的用户。

这是通过将用户 ID 和组 ID 通过以下方式传递给容器来完成的：`USER_ID` 和 `GROUP_ID` 环境变量。

要查找要使用的正确 ID，请在主机上发出以下命令，其中包含拥有主机上数据卷的用户：

    id <username>

其输出如下所示：
```
uid=1000(myuser) gid=1000(myuser) groups=1000(myuser),4(adm),24(cdrom),27(sudo),46(plugdev),113(lpadmin)
```

“uid”（用户 ID）和“gid”（组 ID）的值是您应该给予容器设置的值。

## 安全

默认情况下，对应用程序 GUI 的访问是通过未加密的连接（HTTP 或 VNC）。

可以通过“SECURE_CONNECTION”环境变量启用安全连接。 有关如何设置环境变量的更多详细信息，请参阅[环境变量](#环境变量)部分。

启用后，使用浏览器访问时，应用程序的 GUI 将通过 HTTPs 连接执行。 所有 HTTP 访问都会自动重定向到 HTTP。

使用 VNC 客户端时，VNC 连接通过 SSL 执行。 请注意，很少有 VNC 客户端支持此方法。 [SSVNC] 就是其中之一。

[SSVNC]: http://www.karlrunge.com/x11vnc/ssvnc.html

### 证书

以下是容器所需的证书文件。 默认情况下，当它们丢失时，将生成并使用自签名证书。 所有文件都有 PEM 编码的 x509 证书。

| 容器路径                 | 目的                   | 内容 |
|-----------------------------------|----------------------------|---------|
|`/config/certs/vnc-server.pem`   |VNC 连接加密  |VNC 服务器的私钥和证书，与任何根证书和中间证书捆绑在一起|
|`/config/certs/web-privkey.pem`  |HTTPs 连接加密 |Web 服务器的私钥|
|`/config/certs/web-fullchain.pem`|HTTPs 连接加密 |Web 服务器的证书，与任何根证书和中间证书捆绑在一起|

**注意**：为防止浏览器或 VNC 客户端出现任何证书有效性警告/错误，请确保提供您自己的有效证书。

**注意**：证书文件受到监控，并且当检测到更改时，相关守护进程会自动重新启动。

### VNC 密码

要限制对您的应用程序的访问，可以指定密码。 这可以通过两种方法完成：
   * 通过使用`VNC_PASSWORD`环境变量。
   * 通过在“/config”卷的根目录下创建“.vncpass_clear”文件。 该文件应包含明文形式的密码。 在容器启动期间，文件的内容被混淆并移动到“.vncpass”。

VNC 密码提供的安全级别取决于两件事：
   * 通信通道的类型（加密/未加密）。
   * 对主机的访问有多安全。

使用 VNC 密码时，非常需要启用安全连接，以防止通过未加密的通道以明文形式发送密码。

**注意**：密码不得超过 8 个字符。 此限制来自远程帧缓冲协议 [RFC](https://tools.ietf.org/html/rfc6143)（请参阅第 [7.2.2](https://tools.ietf.org/html/rfc6143#section-7.2.2))。 任何超出限制的字符都将被忽略。

## Shell 访问

要获得对正在运行的容器的 shell 访问权限，请执行以下命令：

```
docker exec -it CONTAINER /bin/sh
```

其中“CONTAINER”是创建期间使用的容器的 ID 或名称。

## 反向代理

以下部分包含需要添加的 NGINX 配置，以便反向代理到此容器。

反向代理服务器可以根据主机名或 URL 路径改变 HTTP 请求。

### 基于主机名的路由选择

在这种情况下，每个主机名都会路由到不同的应用程序/容器。

例如，假设反向代理服务器与该容器运行在同一台机器上。服务器会将发送到 `rclonebrowser.domain.tld` 的所有 HTTP 请求代理到位于 `127.0.0.1:5800` 的容器。

以下是将添加到 NGINX 的相关配置元素配置：

```
map $http_upgrade $connection_upgrade {
	default upgrade;
	''      close;
}

upstream rclonebrowser {
	# If the reverse proxy server is not running on the same machine as the
	# Docker container, use the IP of the Docker host here.
	# Make sure to adjust the port according to how port 5800 of the
	# container has been mapped on the host.
	server 127.0.0.1:5800;
}

server {
	[...]

	server_name rclonebrowser.domain.tld;

	location / {
	        proxy_pass http://rclonebrowser;
	}

	location /websockify {
		proxy_pass http://rclonebrowser;
		proxy_http_version 1.1;
		proxy_set_header Upgrade $http_upgrade;
		proxy_set_header Connection $connection_upgrade;
		proxy_read_timeout 86400;
	}
}

```

### 基于 URL 路径的路由

在这种情况下，主机名相同，但使用不同的 URL 路径路由到不同的应用程序/容器。

例如，假设反向代理服务器与该容器在同一台计算机上运行。 服务器会将“server.domain.tld/rclonebrowser”的所有 HTTP 请求代理到“127.0.0.1:5800”的容器。

以下是将添加到 NGINX 配置中的相关配置元素：

```
map $http_upgrade $connection_upgrade {
	default upgrade;
	''      close;
}

upstream rclonebrowser {
	# If the reverse proxy server is not running on the same machine as the
	# Docker container, use the IP of the Docker host here.
	# Make sure to adjust the port according to how port 5800 of the
	# container has been mapped on the host.
	server 127.0.0.1:5800;
}

server {
	[...]

	location = /rclonebrowser {return 301 $scheme://$http_host/rclonebrowser/;}
	location /rclonebrowser/ {
		proxy_pass http://rclonebrowser/;
		location /rclonebrowser/websockify {
			proxy_pass http://rclonebrowser/websockify/;
			proxy_http_version 1.1;
			proxy_set_header Upgrade $http_upgrade;
			proxy_set_header Connection $connection_upgrade;
			proxy_read_timeout 86400;
		}
	}
}

```

[时区]: http://en.wikipedia.org/wiki/List_of_tz_database_time_zones


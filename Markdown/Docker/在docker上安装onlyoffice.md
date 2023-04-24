# 1、OnlyOffice

## 1.1、基本概念和安装

ONLYOFFICE Document Server API 用于使开发人员将 ONLYOFFICE 文档/电子表格/演示文稿 编辑器集成到他们自己的网站中，并设置和管理编辑器。

这里使用 [Docker](https://www.docker.com/) 进行集成，避免了出现服务器系统的不同而重新适配的问题。Docker的思想来自于集装箱，集装箱解决了什么问题？在一艘大船上，可以把货物规整的摆放起来。并且各种各样的货物被集装箱标准化了，集装箱和集装箱之间不会互相影响。那么我就不需要专门运送水果的船和专门运送化学品的船了。只要这些货物在集装箱里封装的好好的，那我就可以用一艘大船把他们都运走。

**需要了解一下几个概念：镜像，容器，仓库：**

> 镜像（image）：Docker 镜像就是一个只读的模板，镜像可以用来创建 Docker 容器。Docker 提供了一个很简单的机制来创建镜像或者更新现有的镜像，用户甚至可以直接从其他人那里下载一个已经做好的镜像来直接使用。镜像是一种文件结构。Dockerfile中的每条命令都会在文件系统中创建一个新的层次结构，文件系统在这些层次上构建起来，镜像就构建于这些联合的文件系统之上。Docker官方网站专门有一个页面来存储所有可用的镜像，网址是：[index.docker.io](http://index.docker.io/)。
> 容器（ Container）：容器是从镜像创建的运行实例。它可以被启动、开始、停止、删除。每个容器都是相互隔离的、保证安全的平台。可以把容器看做是一个简易版的 Linux 环境，Docker 利用容器来运行应用。
> 仓库：仓库是集中存放镜像文件的场所，仓库注册服务器（Registry）上往往存放着多个仓库，每个仓库中又包含了多个镜像，每个镜像有不同的标签（tag）。目前，最大的公开仓库是 Docker Hub，存放了数量庞大的镜像供用户下载。
Docker 并非是一个通用的容器工具，它依赖于已存在并运行的 Linux 内核环境。它实质上是在已经运行的 Linux 下制造了一个隔离的文件环境，因此它执行的效率几乎等同于所部署的 Linux 主机。因此，Docker 必须部署在 Linux 内核的系统上。如果其他系统想部署 Docker 就必须安装一个虚拟 Linux 环境。

<br>

**Windows 平台上安装 Docker：**

Windows10 中内置了 Linux（WSL），如何打开可以去微软官网 **[查看教程](https://docs.microsoft.com/zh-cn/windows/wsl/install-win10)**。

首先去 Docker 官网下载 **[Docker Desktop](https://desktop.docker.com/win/stable/Docker%20Desktop%20Installer.exe)**，下载完了直接安装。安装好了需要配置一下 Docker 的镜像源，替换为国内源。

<div align="center">
    <img src="../Images/OnlyOffice/image-20210303001708558.png" alt="image-20210303001708558" style="width:80%;" />
</div>

推荐的几个国内源：

```
Docker中国区官方镜像
https://registry.docker-cn.com
网易
http://hub-mirror.c.163.com
ustc 
https://docker.mirrors.ustc.edu.cn
中国科技大学
https://docker.mirrors.ustc.edu.cn
阿里云容器服务(上图所使用的)，去阿里云搜索 容器镜像服务
```

运行测试（dos，powershell，windows terminal都可以，后面的 shell 命令同样）：

```shell
docker -v
```

<br>

Docker 安装好了拉取 Onlyoffice 镜像，创建容器：

```shell
#拉取onlyoffice镜像
docker pull onlyoffice/documentserver
#查看镜像
docker images
#运行镜像（创建容器），并绑定端口，这里绑定 9000
docker run -itd --name office -p 9000:80 onlyoffice/documentserver
#查看正在运行的容器，后面加上 -a 查看所有容器
docker ps
#停止运行
docker stop office
#开始运行
docker start office
#删除镜像（如果有镜像创建的容器需要先删除容器，删除容器需要先停止运行才可以删除）
docker rm office
docker rmi onlyoffice/documentserver
```

查看服务是否已经启动（需要等待一分钟左右）：

```
http://电脑ip:绑定端口
```

<div align="center">
    <img src="../Images/OnlyOffice/image-20210303003314432.png" alt="image-20210303003314432" style="width:100%;" />
</div>

通常可以在以下编辑器文件夹中找到 API JavaScript 文件：

```
http://documentserver/web-apps/apps/api/documents/api.js
```

其中`documentserver`是安装了 ONLYOFFICE Document Server 的服务器的名称。

要嵌入编辑器的目标HTML文件需要有一个占位符div标记，其中将传递有关编辑器参数的所有信息：

```html
<div id="placeholder"></div>
<script type="text/javascript" src="https://documentserver/web-apps/apps/api/documents/api.js"></script>
```

包含可变参数的页面代码是这样的:

```javascript
var docEditor = new DocsAPI.DocEditor("placeholder", config);
```

其中config是一个对象：

```javascript
config = {
    "document": {
        "fileType": "docx",
        "key": "Khirz6zTPdfd7",
        "title": "Example Document Title.docx",
        "url": "https://example.com/url-to-example-document.docx"
    },
    "documentType": "word",
    "editorConfig": {
        "callbackUrl": "https://example.com/url-to-callback.ashx"
    }
};
```

一个简单的例子：

```html
<!DOCTYPE html>
<html style="height: 100%;">
<head>
    <title>ONLYOFFICE Api Documentation</title>
</head>
<body style="height: 100%; margin: 0;">
    <div id="placeholder" style="height: 100%"></div>
    <!--将 documentserver 替换为 安装了 ONLYOFFICE Document Server 的服务器-->
    <script type="text/javascript" src="https://documentserver/web-apps/apps/api/documents/api.js"></script>
    <script type="text/javascript">
        window.docEditor = new DocsAPI.DocEditor("placeholder",
         	{
                "document": {
                    "fileType": "docx",
                    "key": "E7FAFC9C22A8",
                    "title": "Example Document Title.docx",
                    "url": "https://example.com/url-to-example-document.docx" //访问文档的url，自行替换
                },
                "documentType": "word",
                "height": "100%",
                "width": "100%"
            });
    </script>
</body>
</html>
```

<br>

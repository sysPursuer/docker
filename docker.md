# Docker

## 1.Docer三要素



* 前提

* 基本组成

  * 镜像

  * 容器

    | Docker | 面向对象 |
    | ------ | -------- |
    | 镜像   | 类       |
    | 容器   | 对象     |

  * 仓库

Docker本身是一个容器运行载体或称之为管理引擎。我们把应用程序和配置依赖打包好形成一个可交付的环境，这个打包好的运行环境就似乎image镜像文件。只有通过这个镜像文件才能生成Docker容器。image文件可以看作是容器的模板。Docker根据image文件生成容器的实例。同一个image文件，可以生成多个同时运行的容器示例。

一个容器运行一种服务，当我们需要的时候就可以通过docker客户端创建一个对应的运行实例，也就是我们的容器

至于仓库，就是放了一堆镜像文件的地方，我们可以把镜像发布到仓库中，需要的时候从从仓库中拉取下来。

## 2.安装

### 2.1Centos

### 2.2win

## 3.命令

### 3.1镜像命令

* `docker images`列出本地主机上的所有镜像

  ```bash
  options:
  docker images -a:列出本地主机上的所有镜像，含中间映像层
  -q:只显示镜像id
  --digest :显示摘要信息
  --no-trunc ：显示完整镜像信息，不截断
  ```

* `docker search 镜像名`搜索

  * 查询的网站`https://hub.docker.com`

  * 命令

    ```bash
    docker search 镜像名
    -s 20：stars数不小于指定值20的镜像
    
    ```

* `docker pull 镜像名`下载

  ```bash
  docker pull 镜像名：自动拉取最新版
  docker pull 镜像名:8.0 :拉取8.0版本
  ```

* `docker rmi hello-world`不写版本号`:version`，默认为`latest`。删除单个镜像,加`-f`强制删除。

  ```bash
  docker rmi -f 镜像名1:TAG 镜像名2:TAG
  docker rmi -f $(docker images -q)
  ```

### 3.2容器命令

有镜像才能创建容器，这是根本前提

#### 3.2.1简单命令

* 新建并启动容器

  ```bash
  docker run [options] IMAGE [COMMAND] [ARG...]
  OPTIONS
  --name: 对启动的容器别名
  -i: 以交互模式运行容器，通常与-t同时使用
  -t: 为容器重新分配一个伪输入终端，通常与-i同时使用
  -p: 主机端口:docker容器端口
  -P：随机分配(大写P)
  ```



* 列出所有正在运行的容器

  ```bash
  docker ps
  -q: 静默模式，只显示容器编号
  ```

  

* 退出容器

  ```bash
  exit	容器停止退出
  ctrl+p+q 容器不停止退出，以后还希望重新进来
  ```

* 启动容器，启动关闭的容器

  ```bash
  docker start 容器ID或容器名
  ```

* 重启容器

  ```bash
  docker restart 容器id或容器名
  ```

* `docker stop 容器id/名`，停止容器

* `docker kill 容器id/名`，强制停止容器

* `docker rm 容器id`，删除容器

* 一次删除多个

  ```bash
  docker rm -f $(docker ps -a -q)
  docker ps -a -q | xargs docker rm
  ```

#### 3.2.2重要命令

* `docker run -d 容器id`启动守护进程

  ```
  docker ps  -a 发现容器已经退出，docker容器后台运行，就必须有一个前台进程。
  容器运行的命令如果不是那些一直挂起的命令(top,tail),就是会自动退出
  ```

* `docker logs 容器id`查看容器日志

  ```bash
  -t: 按时间
  -f: 不停的追加
  -tail num: 查看后几条
  ```

* `docker top 容器id`查看容器中运行的进程。容器可以看作是一个简易版的linux环境
* `docker inspect 容器id`查看容器内部的细节

* 进入正在运行的容器并以命令行进行交互

  ```sh
  docker exec -it 容器id bashShell：容器执行命令并将结果返回到宿主机docker。是在容器中打开新的终端，并且可以启动新的进程
  docker exec -t 0c9c699b1631 ls -l /tmp
  docker attach 容器id ：重新进入,直接进入容器启动命令的终端，不会启动新的进程
  
  ```

* 从容器内拷贝文件到宿主机上

  ```
  docker cp 容器id:容器内路径 目的主机路径
  ```

  

## 4.镜像原理

​	镜像是一种轻量级、可执行的独立软件包，用来打包软件运行环境和基于运行环境开发的软件，它包含运行某个软件所需的所有内容，包括代码，运行时库、环境变量和配置文件。

### 4.1UnionsFS-联合文件系统

​	Union文件系统是一种分层、轻量级并且高性能的文件系统，它支持对文件系统的修改作为一次提交来一层层的叠加，同时可以将不同目录挂载到同一个虚拟文件系统下。Union文件系统时Docker镜像的基础。镜像可以通过分层来进行继承，基于基础镜像(没有父镜像)，可以制作各种具体的应用镜像。

特性：一次同时加载多个文件系统，但从外面看起来，只能看到一个文件系统，联合加载会把各层文件叠加起来

### 4.2镜像加载原理

docker的镜像实际上由一层一层的文件系统组成，这种层级的文件系统UnionFS。

bootfs主要包含bootloader和kernel，bootloader主要是引导加载kernel，linux刚启动时会加载bootfs文件系统，在Docker镜像的最底层是bootfs.这一层与典型的linux/unix系统是一样的，包含boot加载器和内核。当boot加载完成之后整个内核就都在内存中了，此时内存的使用全已由bootfs转交给内核，此时系统也会卸载bootfs。

rootfs在bootfs之上。包含的是典型Linux系统中的/dev,/proc,/etc等标准目录和文件。rootfs就是各种不同的操作系统发行版。比如ubuntu,centos等。

平时安装的虚拟机的centos都是好几个G,为什么docker这里才200M.

对于一个精简的OS,rootfs可以很小，只需要包括最基本的命令、工具和程序库就可以了，因为底层直接用Host的kernel,自己只需要提供rootfs就行了。由此可见对于不同的linux发行版，bootfs基本是一致的，rootfs会有差别，因此不同的发行版可以公用bootfs.



**Docker 镜像commit操作补充**

* `docker commit`提交容器副本使之成为一个新的镜像

  ```sh
  docker commit -m="提交描述信息" -a="作者" 容器id 要创建的目标镜像名:[标签名]
  ```

* 案例演示

  * 从hub上下载tomcat镜像到本地并成功运行

  * 故意删除上一步镜像的tomcat文档

    ```
    docker exec -it 容器id 
    cd ...
    rm -rf docs
    ```

  * 以没有模板的tocamt，commit一个没有doc的tomcat的新镜像

  * 启动新镜像，进行对比





## 5.容器数据卷


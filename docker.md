# Docker

## Docer三要素



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

## 安装

### Centos

### win

## 命令

### 镜像命令

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

### 容器命令

有镜像才能创建容器，这是根本前提

* 新建并启动容器

  ```bash
  docker run [options] IMAGE [COMMAND] [ARG...]
  OPTIONS
  --name: 对启动的容器别名
  -i: 以交互模式运行容器，通常与-t同时使用
  -t: 为容器重新分配一个伪输入终端，通常与-i同时使用
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

  
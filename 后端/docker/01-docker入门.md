

# Docker

## Docker 安装
-  卸载旧版本
> 旧版本的 Docker 称为 docker 或者 docker-engine，使用以下命令卸载旧版本：

```
sudo apt-get remove docker docker-engine docker-ce docker.io
```
- 使用脚本自动安装
> 在测试或开发环境中 Docker 官方为了简化安装流程，提供了一套便捷的安装脚本，Ubuntu 系统上可以使用这套脚本安装：  

```
$ curl -fsSL get.docker.com -o get-docker.sh
# 可能会出现 404 错误，请移步下面的特别说明
$ sudo sh get-docker.sh --mirror Aliyun
```
执行这个命令后，脚本就会自动的将一切准备工作做好，并且把 Docker CE 的 Edge 版本安装在系统中

- 启动 Docker 命令
Ubuntu 16.04
```
$ sudo systemctl enable docker
$ sudo systemctl start docker
```
Ubuntu 14.04 请使用以下命令启动：
```
$ sudo service docker start
```
查看docker的安装版本：
```
root@ubuntu:~# docker version

Client: Docker Engine - Community
 Version:           19.03.8
 API version:       1.40
 Go version:        go1.12.17
 Git commit:        afacb8b7f0
 Built:             Wed Mar 11 01:25:58 2020
 OS/Arch:           linux/amd64
 Experimental:      false

Server: Docker Engine - Community
 Engine:
  Version:          19.03.8
  API version:      1.40 (minimum version 1.12)
  Go version:       go1.12.17
  Git commit:       afacb8b7f0
  Built:            Wed Mar 11 01:24:30 2020
  OS/Arch:          linux/amd64
  Experimental:     false
 containerd:
  Version:          1.2.13
  GitCommit:        7ad184331fa3e55e52b890ea95e65ba581ae3429
 runc:
  Version:          1.0.0-rc10
  GitCommit:        dc9208a3303feef5b3839f4323d9beb36df0a9dd
 docker-init:
  Version:          0.18.0
  GitCommit:        fec3683
```
- Docker 镜像加速器
> 国内从 Docker Hub 拉取镜像有时会遇到困难，此时可以配置镜像加速器。Docker 官方和国内很多云服务商都提供了国内加速器服务，例如：  

- [Docker 官方提供的中国 registry mirror](https://docs.docker.com/registry/recipes/mirror/#use-case-the-china-registry-mirror)  
- [阿里云加速器](https://account.aliyun.com/login/login.htm?oauth_callback=https%3A%2F%2Fcr.console.aliyun.com%2F#/accelerator)  
- [DaoCloud 加速器](https://www.daocloud.io/mirror#accelerator-doc)  

> 下面使用Docker 官方加速器为例进行介绍。  

> 对于使用 systemd 的系统，请在 /etc/docker/daemon.json 中写入如下内容（如果文件不存在请新建该文件）
```
{
  "registry-mirrors": [
    "https://registry.docker-cn.com"
  ]
}
# 注意，一定要保证该文件符合 json 规范，否则 Docker 将不能启动。
```

之后重新启动服务。  
```
$ sudo systemctl daemon-reload
$ sudo systemctl restart docker
```
- 检查加速器是否生效  

> 配置加速器之后，如果拉取镜像仍然十分缓慢，请手动检查加速器配置是否生效，在命令行执行 docker info，如果从结果中看到了如下内容，说明配置成功。  

```
Registry Mirrors:
 https://registry.docker-cn.com/
```
## docker 命令
### 获取镜像
> 从 Docker 镜像仓库获取镜像的命令是 docker pull。其命令格式为：
```
docker pull [选项] [Docker Registry 地址[:端口号]/]仓库名[:标签]
```

- Docker 镜像仓库地址：  

> 地址的格式一般是 <域名/IP>[:端口号]。默认地址是 Docker Hub。

- 仓库名:

> 如之前所说，这里的仓库名是两段式名称，即 <用户名>/<软件名>。对于 Docker Hub，如果不给出用户名，则默认为 library，也就是官方镜像。

- 例子：
```
root@ubuntu:/# docker pull ubuntu:16.04
16.04: Pulling from library/ubuntu
bf5d46315322: Pull complete
9f13e0ac480c: Pull complete
e8988b5b3097: Pull complete
40af181810e7: Pull complete
e6f7c7e5c03e: Pull complete
Digest: sha256:147913621d9cdea08853f6ba9116c2e27a3ceffecf3b492983ae97c3d643fbbe
Status: Downloaded newer image for ubuntu:16.04
root@ubuntu:/# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
ubuntu              16.04               005d2078bdfa        8 days ago          125MB
```
分析：  
> 上面的命令中没有给出 Docker 镜像仓库地址，因此将会从 Docker Hub 获取镜像。而镜像名称是 ubuntu:16.04，因此将会获取官方镜像 library/ubuntu 仓库中标签为 16.04 的镜像。  
> 有了镜像后，我们就能够以这个镜像为基础启动并运行一个容器。以上面的 ubuntu:16.04 为例，如果我们打算启动里面的 bash 并且进行交互式操作的话，可以执行下面的命令。

- 运行ubuntu
```
root@ubuntu:/# docker run -it --rm \   # 当需要输入多个命令的时候可以使用 \ 进行换行  
> ubuntu:16.04 \
> bash
docker run 表示运行一个镜像，-t --rm 代表参数 ubuntu:16.04 表示运行哪个镜像 bash表示运行Ubuntu镜像的bash程序
root@07e8db710573:/# 
发现进入到用一个新的ubuntu，即docker的Ubuntu系统中去了
```
上面例子的分析：  
- `-it`：这是两个参数，一个是 `-i`：交互式操作; 一个是 `-t` 终端。我们这里打算进入 `bash` 执行一些命令并查看返回结果，因此我们需要交互式终端。  
- `--rm`：这个参数是说容器退出后随之将其删除。默认情况下，为了排障需求，退出的容器并不会立即删除，除非手动 `docker rm`。我们这里只是随便执行个命令，看看结果，不需要排障和保留结果，因此使用 `--rm` 可以避免浪费空间。
```
现在如果我们想要退出系统，可以使用快捷键 ctrl+D, 或者输入exit命令
root@07e8db710573:/# exit
root@ubuntu:/# 
发现已经从docker的Ubuntu系统中退出去了，而且我们查看docker 里面的镜像和容器
root@ubuntu:/# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
tomcat              latest              927899a31456        7 days ago          647MB
ubuntu              16.04               005d2078bdfa        8 days ago          125MB
root@ubuntu:/# docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
发现容器没有了，可能还没发现，我们试试如果没有带--rm参数试一下
root@ubuntu:/# docker run -it  ubuntu:16.04 bash
root@f653cef21a1d:/# exit
exit
root@ubuntu:/# docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
# 发现docker ps 是没有容器，但是ps这个命令只是查看正在运行的容器
root@ubuntu:/# docker ps -a
# 使用docker ps -a查看所有的容器
\CONTAINER ID        IMAGE               COMMAND             CREATED              STATUS                          PORTS               NAMES
f653cef21a1d        ubuntu:16.04        "bash"              About a minute ago   Exited (0) About a minute ago                       ecstatic_benz
# 发现容器还存在，即相当于一个镜像类创建了一个容器对象，但是对象使用完后，对象还存在，为了节约资源，我们可以手动的回收这个对象，所以可以在开始的时候就使用 --rm 参数即可 
# 如果一个容器不用了，就应该把它删掉 使用docker rm 容器id
root@ubuntu:/# docker rm f653cef21a1d
f653cef21a1d
root@ubuntu:/# docker ps -a
CONTAINER ID        IMAGE               COMMAND             CREATED 
root@ubuntu:/# 
```
- `ubuntu:16.04`：这是指用 `ubuntu:16.04` 镜像为基础来启动容器。  
- `bash`：放在镜像名后的是命令，这里我们希望有个交互式 `Shell`，因此用的是 `bash`。
- 进入容器后，我们可以在 `Shell` 下操作，执行任何所需的命令。这里，我们执行了 `cat /etc/os-release`，这是 Linux 常用的查看当前系统版本的命令，从返回的结果可以看到容器内是 `Ubuntu 16.04.4 LTS` 系统。  

### 进入容器
当我们启动一个容器，可以使用 `docker ps `查看正在运行的容器，然后也可以使用`docker ps -a`查看所有的容器。对于启动中的容器我们可以使用
```
docker exec -it c7426a0763f6 bash 
# 以交互的方式重新进入到一个已经启动的容器
docker start c7426a0763f6
# 重新启动一个已经停止了的容器
docker stop c7426a0763f6
# 停止一个正在运行的容器
docker restart c7426a0763f6
# 重新启动一个正在运行的容器
```

### 列出镜像
> 要想列出已经下载下来的镜像，可以使用 `docker image ls` 命令。

### 虚悬镜像
> 有一种特殊的镜像，这个镜像既没有仓库名，也没有标签，均为 `<none>`。：

```
$ docker image ls
REPOSITORY           TAG                 IMAGE ID            CREATED             SIZE
<none>               <none>              00285df0df87        5 days ago          342 MB
```
> 这个镜像原本是有镜像名和标签的，原来为 `mongo:3.2`，随着官方镜像维护，发布了新版本后，重新 `docker pull mongo:3.2`时，`mongo:3.2` 这个镜像名被转移到了新下载的镜像身上，而旧的镜像上的这个名称则被取消，从而成为了 `<none>`。  

> 除了 `docker pull` 可能导致这种情况，`docker build` 也同样可以导致这种现象。由于新旧镜像同名，旧镜像名称被取消，从而出现仓库名、标签均为 `<none>` 的镜像。这类无标签镜像也被称为 虚悬镜像(`dangling image`) ，可以用下面的命令专门显示这类镜像：

```
$ docker image ls -f dangling=true
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
<none>              <none>              00285df0df87        5 days ago          342 MB
```
一般来说，虚悬镜像已经失去了存在的价值，是可以随意删除的，可以用下面的命令删除。
```
$ docker image prune
```
- 例子：如果我们开始下载了`Ubuntu：16.04`然后再过几天，因为官方这个`Ubuntu:16.04`版本进行了漏洞更新，然后我们再次`docker pull Ubuntu:16.04 `的时候,由于进行了更新，导致我们再次pull的时候会把最新的pull下来，然后原来的就会成为虚悬镜像。  

### 删除本地镜像
如果要删除本地的镜像，可以使用 docker image rm 命令，其格式为：  
```
$ docker image rm [选项] <镜像1> [<镜像2> ...]
```
我们可以用 ID、镜像名、摘要删除镜像，上面的格式中的，<镜像> 可以是 镜像短 ID、镜像长 ID、镜像名 或者 镜像摘要。  
> 我们可以用镜像的完整 ID，也称为 长 ID，来删除镜像。  
> 使用脚本的时候可能会用长 ID，但是人工输入就太累了，所以更多的时候是用 短 ID 来删除镜像。  

> docker image ls 默认列出的就已经是短 ID 了，一般取前3个字符以上，只要足够区分于别的镜像就可以了。  

```
$ docker image ls
REPOSITORY                  TAG                 IMAGE ID            CREATED             SIZE
redis                       alpine              501ad78535f0        3 weeks ago         21.03 MB
```
> 比如这里，如果我们要删除 redis:alpine 镜像，可以执行：

```
$ docker image rm 501
Untagged: redis:alpine
Untagged: redis@sha256:f1ed3708f538b537eb9c2a7dd50dc90a706f7debd7e1196c9264edeea521a86d
Deleted: sha256:501ad78535f015d88872e13fa87a828425117e3d28075d0c117932b05bf189b7
Deleted: sha256:96167737e29ca8e9d74982ef2a0dda76ed7b430da55e321c071f0dbff8c2899b
Deleted: sha256:32770d1dcf835f192cafd6b9263b7b597a1778a403a109e2cc2ee866f74adf23
Deleted: sha256:127227698ad74a5846ff5153475e03439d96d4b1c7f2a449c7a826ef74a2d2fa
Deleted: sha256:1333ecc582459bac54e1437335c0816bc17634e131ea0cc48daa27d32c75eab3
Deleted: sha256:4fc455b921edf9c4aea207c51ab39b10b06540c8b4825ba57b3feed1668fa7c7
```
> 我们也可以用镜像名，也就是 <仓库名>:<标签>，来删除镜像。

```
$ docker image rm centos
Untagged: centos:latest
Untagged: centos@sha256:b2f9d1c0ff5f87a4743104d099a3d561002ac500db1b9bfa02a783a46e0d366c
Deleted: sha256:0584b3d2cf6d235ee310cf14b54667d889887b838d3f3d3033acd70fc3c48b8a
Deleted: sha256:97ca462ad9eeae25941546209454496e1d66749d53dfa2ee32bf1faabd239d38  
也可以直接 docker rmi centos / docker rmi centos:latest
```

### docker 常用命令总结
- 查看 Docker 版本
```
docker version
```
- 从 Docker 文件构建 Docker 映像
```
docker build -t image-name docker-file-location
```
- 运行 Docker 映像
```
docker run -d image-name
```
- 查看可用的 Docker 映像
```
docker images
```
- 查看最近的运行容器
```
docker ps -l
```
- 查看所有正在运行的容器
```
docker ps -a
```
- 停止运行容器
```
docker stop container_id
```
- 删除一个镜像
```
docker rmi image-name
```
- 删除所有镜像
```
docker rmi $(docker images -q)
```
- 强制删除所有镜像
```
docker rmi -r $(docker images -q)
```
- 删除所有虚悬镜像
```
docker rmi $(docker images -q -f dangling=true)
```
- 删除所有容器
```
docker rm $(docker ps -a -q)
```
- 进入 Docker 容器
```
docker exec -it container-id /bin/bash
```
- 查看所有数据卷
```
docker volume ls
```
- 删除指定数据卷
```
docker volume rm [volume_name]
```
- 删除所有未关联的数据卷
```
docker volume rm $(docker volume ls -qf dangling=true)
```
- 从主机复制文件到容器
```
sudo docker cp host_path containerID:container_path
```
- 从容器复制文件到主机
```
sudo docker cp containerID:container_path host_path
```
## 使用Dockerfile来定制镜像
> Dockerfile 是一个文本文件，其内包含了一条条的指令(Instruction)，每一条指令构建一层，因此每一条指令的内容，就是描述该层应当如何构建。  

> 还以之前定制 tomcat 镜像为例，这次我们使用 Dockerfile 来定制。  

实验  
- 先运行tomcat
```
root@ubuntu:~# docker run -P b56d8850aed5(镜像id) 
```
- 然后进入到容器里面进行修改
```
root@ubuntu:~# docker exec -it 61cd845e42ce(容器id) bash  

# 不能再次使用 docker run -it 61cd845e42ce(容器id) bash,因为run命令会新建一个容器运行，相当于新建一个对象一样  
```
- 修改index.jsp文件
```
root@98d12fd38fb3:/usr/local/tomcat/webapps/ROOT# echo "hello" >> index.jsp
```
- 访问发现修改了jsp文件

构建一个自己的tomcat镜像,然后里面部署一个index.html页面：
- 在`usr/local`目录下新建一个`tomcat`文件夹新建一个`Dockerfile`的文件
- 编写文件的内容
```
FROM tomcat:8.5.50
# 所有的Dockerfile文件必须要以FROM命令开头，表示一个基础镜像，在之前必须先连接基础镜像的目录结构（以交互的方式进入到容器中的时候的初始目录就是该镜像的工作目录）  
RUN rm -fr /usr/local/tomcat/webapps
RUN mv /usr/local/tomcat/webapps.dist /usr/local/tomcat/webapps
RUN echo "hello docker" > /usr/local/tomcat/webapps/ROOT/index.html
```
> 1. FROM 指定基础镜像  
> 所谓定制镜像，那一定是以一个镜像为基础，在其上进行定制。  
> 2. RUN 执行命令
> RUN 指令是用来执行命令行命令的。由于命令行的强大能力，RUN 指令在定制镜像时是最常用的指令之一。其格式有两种：  
> - `shell` 格式：`RUN <命令>`，就像直接在命令行中输入的命令一样。刚才写的 `Dockerfile` 中的 `RUN` 指令就是这种格式。  
> - `exec` 格式：`RUN ["可执行文件", "参数1", "参数2"]`，这更像是函数调用中的格式。  
> `Dockerfile` 中每一个指令都会建立一层，`RUN` 也不例外。每一个 `RUN` 的行为，就和刚才我们手工建立镜像的过程一样：新建立一层，在其上执行这些命令，执行结束后，`commit` 这一层的修改，构成新的镜像。  

- 构建镜像  
**注意**：需要在 `Dockerfile` 文件所在目录执行构建语句：
```
root@ubuntu:/usr/local/docker/tomcat# docker build -t mytest .
Sending build context to Docker daemon  2.048kB
Step 1/4 : FROM tomcat:8.5.50
 ---> b56d8850aed5
Step 2/4 : RUN rm -fr /usr/local/tomcat/webapps
 ---> Running in 1031a5b6a46d
Removing intermediate container 1031a5b6a46d
 ---> ed2bda282937
Step 3/4 : RUN mv /usr/local/tomcat/webapps.dist /usr/local/tomcat/webapps
 ---> Running in 18f09f9cee18
Removing intermediate container 18f09f9cee18
 ---> 42bdcd9947de
Step 4/4 : RUN echo "hello docker" > /usr/local/tomcat/webapps/ROOT/index.html
 ---> Running in 5a432e84f1a2
Removing intermediate container 5a432e84f1a2
 ---> f65e95edf0f2
Successfully built f65e95edf0f2
Successfully tagged mytest:latest 
# 从命令的输出结果中，我们可以清晰的看到镜像的构建过程。
# 在 Step 2 中，如同我们之前所说的那样，RUN 指令启动了一个容器，执行了所要求的命令，并最后提交了这一层，随后删除了所用到的这个容器。
```
> 这里我们使用了 `docker build` 命令进行镜像构建。其格式为：

```
docker build [选项] 镜像名 <上下文路径/URL/->
```

对于Dockerfile的文件内容：
```
FROM tomcat:8.5.50
RUN rm -fr /usr/local/tomcat/webapps
RUN mv /usr/local/tomcat/webapps.dist /usr/local/tomcat/webapps
RUN echo "hello docker" > /usr/local/tomcat/webapps/ROOT/index.html
```
可能有疑问的是，我们可以先执行`cd`到`/usr/local/tomcat/`的目录下，然后在以这个目录为基础进行下面的操作，可能就不需要写那么多内容了
```
FROM tomcat:8.5.50
RUN cd /usr/local/tomcat
RUN rm -fr webapps
RUN mv webapps.dist webapps
RUN echo "hello docker" > webapps/ROOT/index.html
```
可能上面的步骤会发生错误，因为docker在每次执行的时候都是相对重新开始的，这个时候我们需要使用`WORKDIR`指令
```
FROM tomcat:8.5.50

WORKDIR cd /usr/local/tomcat
RUN rm -fr webapps
RUN mv webapps.dist webapps
RUN echo "hello docker" > webapps/ROOT/index.html

# 发现指定`WORKDIR`后，我们在进入到容器中的时候，默认进入到的初始目录就是这个指定的工作目录
# 可以告诉用户整个项目安装在哪儿  
```
然后再重新构建镜像
```
root@ubuntu:/usr/local/docker/tomcat# docker build -t mytest .
```
如果重新构建一样的镜像名的镜像，那么原来的镜像就会变成虚悬镜像  

### 镜像构建上下文（`Context`）
> 如果注意，会看到 `docker build` 命令最后有一个 `.`。`.`表示当前目录，而 `Dockerfile` 就在当前目录，因此不少初学者以为这个路径是在指定 `Dockerfile` 所在路径，这么理解其实是不准确的。如果对应上面的命令格式，你可能会发现，这是在指定上下文路径。  

- `docker build` 的工作原理:  
`Docker` 在运行时分为 `Docker` 引擎（也就是服务端守护进程）和客户端工具。`Docker` 的引擎提供了一组 REST API，被称为 `Docker Remote API`，而如 `docker` 命令这样的客户端工具，则是通过这组 `API` 与 `Docker` 引擎交互，从而完成各种功能。因此，虽然表面上我们好像是在本机执行各种 `docker` 功能，但实际上，一切都是使用的远程调用形式在服务端（`Docker` 引擎）完成。也因为这种 `C/S` 设计，让我们操作远程服务器的 `Docker` 引擎变得轻而易举  

&nbsp;&nbsp;&nbsp;&nbsp;当我们进行镜像构建的时候，并非所有定制都会通过 `RUN` 指令完成，经常会需要将一些本地文件复制进镜像，比如通过 `COPY` 指令、ADD 指令等。而 `docker build` 命令构建镜像，==其实并非在本地构建，而是在服务端，也就是 `Docker` 引擎中构建的==。那么在这种客户端/服务端的架构中，如何才能让服务端获得本地文件呢？  
&nbsp;&nbsp;&nbsp;&nbsp;这就引入了上下文的概念。当构建的时候，用户会指定构建镜像上下文的路径，`docker build` 命令得知这个路径后，==会将路径下的所有内容打包，然后上传给 `Docker` 引擎==。这样 `Docker` 引擎收到这个上下文包后，展开就会获得构建镜像所需的一切文件。  
理解：  
&nbsp;&nbsp;&nbsp;&nbsp;假如现在有一个文件夹里面有2个文件，一个 `Dockerfile`文件，一个`test.txt`文件，然后我们在使用`docker build`的时候，默认会把这个目录的所有文件打包，假如打包成`my.zip`，然后发送到`docker`的服务端，自动解压成打包前的样子，这样我们在`dockerfile`文件中就可以操作这些打包进的文件了。  

如果在 Dockerfile 中这么写：
```
COPY ./package.json /app/
```
&nbsp;&nbsp;&nbsp;&nbsp;这并不是要复制执行 `docker build` 命令所在的目录下的 `package.json`，也不是复制 `Dockerfile` 所在目录下的 `package.json`，而是复制 上下文（`context`） 目录下的 `package.json`。  
&nbsp;&nbsp;&nbsp;&nbsp;因此，`COPY` 这类指令中的源文件的路径都是相对路径。这也是初学者经常会问的为什么 `COPY ../package.json /app` 或者 `COPY /opt/xxxx /app` 无法工作的原因，因为这些路径已经超出了上下文的范围，`Docker` 引擎无法获得这些位置的文件。如果真的需要那些文件，应该将它们复制到上下文目录中去。  
&nbsp;&nbsp;&nbsp;&nbsp;现在就可以理解刚才的命令 `docker build -t nginx:v3 .` 中的这个 `.`，实际上是在指定上下文的目录，`docker build` 命令会将该目录下的内容打包交给 `Docker` 引擎以帮助构建镜像。  
&nbsp;&nbsp;&nbsp;&nbsp;理解构建上下文对于镜像构建是很重要的，避免犯一些不应该的错误。比如有些初学者在发现 `COPY /opt/xxxx /app` 不工作后，于是干脆将 `Dockerfile` 放到了硬盘根目录去构建，结果发现 `docker build` 执行后，在发送一个几十 GB 的东西，极为缓慢而且很容易构建失败。那是因为这种做法是在让 `docker build` 打包整个硬盘，这显然是使用错误。  
&nbsp;&nbsp;&nbsp;&nbsp;一般来说，应该会将 `Dockerfile` 置于一个空目录下，或者项目根目录下。如果该目录下没有所需文件，那么应该把所需文件复制一份过来。如果目录下有些东西确实不希望构建时传给 `Docker` 引擎，那么可以用 `.gitignore` 一样的语法写一个 `.dockerignore`，该文件是用于剔除不需要作为上下文传递给 `Docker` 引擎的。  
&nbsp;&nbsp;&nbsp;&nbsp;那么为什么会有人误以为 . 是指定 `Dockerfile` 所在目录呢？这是因为在默认情况下，如果不额外指定 `Dockerfile` 的话，会将上下文目录下的名为 `Dockerfile` 的文件作为 `Dockerfile`。  
> 当然，**一般大家习惯性的会使用默认的文件名 Dockerfile，以及会将其置于镜像构建上下文目录中**。

实验:在`dockerfile`文件中新增加一个`index.html`，然后在`dockerfile`文件中将这个`index.html`文件复制到`tomcat`的`ROOT`目录下  
- 在`usr/local/`目录下新建一个新的tomcat文件夹，专门用来定制镜像  
- 在该文件下新建一个`Dockerfile`文件和一个`index.html`文件
- 编写`Dockerfile`文件:
```
# 基础镜像为tomcat
FROM tomcat:8.5.50
# 切换工作目录
WORKDIR /usr/local/tomcat
# 下面的2个目录只是不知道为什么webapps的内容都到webapps.dist文件中
RUN rm -fr webapps
RUN mv webapps.dist webapps
# COPY 后面的index.html文件的目录是一个相对目录，相对于上下文的目录
COPY  index.html  webapps/ROOT
```

### Dockerfile 指令
#### COPY
格式：  
> - `COPY <源路径>... <目标路径>`
> - `COPY ["<源路径1>",... "<目标路径>"]`  

`COPY` 指令将从构建上下文目录中 `<源路径>` 的文件/目录复制到新的一层的镜像内的 `<目标路径>` 位置。  
`<源路径>` 指上下文目录中的路径，是一个相对路径,**相对上下文**，可以是多个，甚至可以是通配符，如：
```
COPY hom* /mydir/
COPY hom?.txt /mydir/
```
`<目标路径>` 可以是容器内的绝对路径，也可以是相对于**工作目录的相对路径**（工作目录可以用` WORKDIR` 指令来指定）。目标路径不需要事先创建，如果目录不存在会在复制文件前先行创建缺失目录。  
此外，还需要注意一点，使用 `COPY` 指令，源文件的各种元数据都会保留。比如读、写、执行权限、文件变更时间等。这个特性对于镜像定制很有用。特别是构建相关文件都在使用 `Git` 进行管理的时候。  
实验：在上下文的路径下方一个`my.zip`压缩文件夹，里面有一个`index.html`,还有一个`test.html`文件，现在想部署这个项目到`tomcat`  
- 将这个`zip`包上传到`Ubuntu`的`dockerfile`所在的文件夹中
- 编写`Dockerfile`文件
```
# 基础镜像为tomcat
FROM tomcat:8.5.50
# 切换工作目录
WORKDIR /usr/local/tomcat
# 下面的2个目录只是不知道为什么webapps的内容都到webapps.dist文件中
RUN rm -fr webapps
RUN mv webapps.dist webapps
# 将上下文目录下的my.zip文件夹拷贝到工作目录下的webapps/ROOT目录下
COPY  my.zip  webapps/ROOT
# 先切换工作目录，不然下面在解压缩的时候会解压到上面指定的压缩目录上面去了
WORKDIR /usr/local/tomcat/webapps/ROOT
# 压缩my.zip文件，zip压缩文件解压要用unzip命令
RUN unzip my.zip
```
- 构建镜像
```
root@ubuntu:/usr/local/docker/tomcat# docker build -t my .
Sending build context to Docker daemon  3.072kB
Step 1/6 : FROM tomcat:8.5.50
 ---> b56d8850aed5
Step 2/6 : WORKDIR /usr/local/tomcat
 ---> Using cache
 ---> 4a225c022572
Step 3/6 : RUN rm -fr webapps
 ---> Using cache
 ---> c724e11d4a16
Step 4/6 : RUN mv webapps.dist webapps
 ---> Using cache
 ---> 3c89b8215c5b
Step 5/6 : COPY  my.zip  webapps/ROOT
 ---> Using cache
 ---> cd3150ff67f2
Step 6/6 : RUN unzip webapps/ROOT/my.zip
 ---> Running in 869eb86b9f73
Archive:  webapps/ROOT/my.zip
 extracting: index.html              
 extracting: test.html               
Removing intermediate container 869eb86b9f73
 ---> 47781daf5ceb
Successfully built 47781daf5ceb
Successfully tagged my:latest
```
- 运行镜像
```
docker run -P mytest .
```

#### ADD
`ADD` 指令和 `COPY` 的格式和性质基本一致。但是在 `COPY` 基础上增加了一些功能。  
- 比如 `<源路径>`可以是一个 URL，这种情况下，Docker 引擎会试图去下载这个链接的文件放到 `<目标路径>` 去。下载后的文件权限自动设置为 600，如果这并不是想要的权限，那么还需要增加额外的一层 RUN 进行权限调整，另外，如果下载的是个压缩包，需要解压缩，也一样还需要额外的一层 `RUN` 指令进行解压缩。 
- 如果 `<源路径>` 为一个 tar 压缩文件的话，压缩格式为 `gzip`, `bzip2` 以及 `xz` 的情况下，`ADD` 指令将会自动解压缩这个压缩文件到 `<目标路径>` 去,并且还会将这个原有的压缩文件自动删除。  
- 另外需要注意的是，`ADD` 指令会令镜像构建缓存失效，从而可能会令镜像构建变得比较缓慢。

【总结】因此在 `COPY` 和 `ADD` 指令中选择的时候，可以遵循这样的原则，所有的文件复制均使用 `COPY` 指令，仅在需要自动解压缩的场合使用 `ADD`。  

#### EXPOSE
> 格式为:  `EXPOSE <端口1> [<端口2>...]`  

`EXPOSE` 指令是声明运行时容器提供服务端口，这只是一个声明，在运行时并不会因为这个声明应用就会开启这个端口的服务  
在 `Dockerfile` 中写入这样的声明有两个好处，一个是帮助镜像使用者理解这个镜像服务的守护端口，以方便配置映射；另一个用处则是在运行时使用随机端口映射时，也就是 `docker run -P` 时，会==自动随机映射== `EXPOSE` 的端口  
要将 `EXPOSE` 和在运行时使用 `-p <宿主端口>:<容器端口>` 区分开来。`-p`，是映射宿主端口和容器端口，换句话说，就是将容器的对应端口服务公开给外界访问，而 `EXPOSE` 仅仅是声明容器打算使用什么端口而已，并不会自动在宿主进行端口映射。  

#### CMD
> `CMD` 指令的格式和 `RUN` 相似，也是两种格式： 

- `shell` 格式：`CMD <命令>  `
- `exec` 格式：`CMD ["可执行文件", "参数1", "参数2"...]`  
参数列表格式：`CMD ["参数1", "参数2"...]`。在指定了 `ENTRYPOINT` 指令后，用` CMD` 指定具体的参数。  

在运行时可以指定新的命令来替代镜像设置中的这个默认命令，比如，`ubuntu` 镜像默认的 `CMD` 是 `/bin/bash`，如果我们直接 `docker run -it ubuntu` 的话，会直接进入 `bash`。我们也可以在运行时指定运行别的命令，如 `docker run -it ubuntu cat /etc/os-release`。这就是用 `cat /etc/os-release` 命令替换了默认的 `/bin/bash` 命令了，输出了系统版本信息。  

### 守护态运行容器
更多的时候，需要让 `Docker` 在后台运行而不是直接把执行命令的结果输出在当前宿主机下。此时，可以通过添加 `-d` 参数来实现。 
```
$ docker run -P tomcat -d tomcat 
77b2dc01fe0f3f1265df143181e7b9af5e05279a884f4776ee75350ea9d8017a
```

此时容器会在后台运行并不会把输出的结果 (`STDOUT`) 打印到宿主机上面(输出结果可以用 `docker logs` 查看)。  
```
$ docker container logs [container ID or NAMES]
hello world
hello world
hello world
. . .
```

## 数据卷
`数据卷` 是一个可供一个或多个容器使用的特殊目录，它绕过 `UFS`，可以提供很多有用的特性：  
- `数据卷` 可以在容器之间共享和重用
- 对 `数据卷` 的修改会立马生效
- 对 `数据卷` 的更新，不会影响镜像
- `数据卷` 默认会一直存在，即使容器被删除  

例子：启动一个`tomcat`,而不是像之前那样将`index.html`直接放到ROOT的目录下，而是使用共享数据卷共享宿主机的`ROOT`目录和`docker`的`tomcat`的`ROOT`目录的方式  
- 在宿主机的一个目录下新建一个`ROOT`目录，里面放一个`index.html`  
- 然后就可一启动一个干净的tomcat容器，然后干净的`tomcat`目录的`ROOT`目录使用的是宿主机下的一个`ROOT`目录  
```
root@ubuntu:/usr/local/docker/ROOT# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
mytomcat            latest              ab1e58c16d85        11 minutes ago      652MB
tomcat              latest              927899a31456        10 days ago         647MB
root@ubuntu:/usr/local/docker/ROOT# docker run -P -v /usr/local/docker/ROOT:/usr/local/tomcat/webapps/ROOT mytomcat

# 上面的mytomcat其实就是一个干净的tomcat，因为里面的webapps的目录下的文件被webapps.dist下替换
# 只不过只是将webapps.dist下的文件全部都移动到webapps目录下 
# -v : 表示需要挂载数据卷
# 文件1:文件2 ：文件1表示的是宿主机里面的目录文件，然后右边的是docker 镜像中的目录
# 换句话说就是用宿主机的那个目录替换镜像中的那个文件  
# 发现如果我们修改宿主机中的index.html中内容，然后docker 镜像中的文件也会随着改变，这就是共享  
```
- 然后我们还可以再启动一个tomcat
```
root@ubuntu:/usr/local/docker/ROOT# docker run -P -v /usr/local/docker/ROOT:/usr/local/tomcat/webapps/ROOT mytomcat
```
发现还是使用到宿主机中的ROOT数据，这样就达到了多个容器共享到宿主机中的数据卷文件  

### 数据卷之mysql
- 拉取数据库
```
root@ubuntu:~# docker pull mysql:5.7.22
```
> 虽然mysql已经出现了8，而且速度也非常快，但不排除一些项目还是用5.7的版本  

- 运行容器
```
root@ubuntu:~# docker run -p 3306:3306 --name mysql \
> -v /usr/local/docker/mysql/conf:/etc/mysql \
> -v /usr/local/docker/mysql/logs:/var/log/mysql \
> -v /usr/local/docker/mysql/data:/var/lib/mysql \
> -e MYSQL_ROOT_PASSWORD=123456 \
> -d mysql:5.7.22
670bef6847a1ee80183dc66b7772d6d1e870cc6d893720434365c87e31988160
root@ubuntu:~# docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                    NAMES
670bef6847a1        mysql:5.7.22        "docker-entrypoint.s…"   7 seconds ago       Up 4 seconds        0.0.0.0:3306->3306/tcp   mysql
```
命令参数：  
> - `-p 3306:3306`：将容器的3306端口映射到主机的3306端口  
> - `--name mysql`: 为容器起一个名字叫mysql
> `-v /usr/local/docker/mysql/conf:/etc/mysql`：将主机当前目录下的 `conf` 挂载到容器的 `/etc/mysql`  
> 如果没有写这句话，那么docker 镜像到时使用的配置文件还是容器中的配置    
> - `-v /usr/local/docker/mysql/logs:/var/log/mysql`：将主机当前目录下的 `logs` 目录挂载到容器的 `/var/log/mysql`  
> - `-v /usr/local/docker/mysql/data:/var/lib/mysql`：将主机当前目录下的 `data` 目录挂载到容器的 `/var/lib/mysql`  
> - `-e MYSQL\_ROOT\_PASSWORD=123456`：初始化`root`用户的密码  

- 使用数据连接软件进行测试连接  
- 疑问：
> 发现我们在`docker`的目录并未创建`mysql`的目录，更别说里面还有`conf`(配置文件)，`logs`(日志)，`data`(数据库数据). 那么使用数据卷是怎么挂载到docker容器中的数据库中的呢  
> 我们可以进入到宿主机中的这个目录中看看：
```
root@ubuntu:~# cd /usr/local/docker
root@ubuntu:/usr/local/docker# ll
total 20
drwxr-xr-x  5 root root 4096 May  5 16:19 ./
drwxr-xr-x 11 root root 4096 May  5 13:51 ../
drwxr-xr-x  5 root root 4096 May  5 16:19 mysql/
drwxr-xr-x  2 root root 4096 May  5 14:08 ROOT/
drwxr-xr-x  2 root root 4096 May  5 13:53 tomcat/
root@ubuntu:/usr/local/docker# cd mysql
root@ubuntu:/usr/local/docker/mysql# ll
total 20
drwxr-xr-x 5 root root   4096 May  5 16:19 ./
drwxr-xr-x 5 root root   4096 May  5 16:19 ../
drwxr-xr-x 2 root root   4096 May  5 16:19 conf/
drwxr-xr-x 5  999 docker 4096 May  5 16:19 data/
drwxr-xr-x 2 root root   4096 May  5 16:19 logs/
```
发现帮我们创建了这3个目录，而且里面还有对应的信息文件。  
然后发现`-v` 然后，`:` 右边的内容是安装mysql时的位置信息，我们可以进入到这个容器中，查看
```
root@ubuntu:/usr/local/docker/mysql/data# docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                    NAMES
670bef6847a1        mysql:5.7.22        "docker-entrypoint.s…"   10 minutes ago      Up 10 minutes       0.0.0.0:3306->3306/tcp   mysql
root@ubuntu:/usr/local/docker/mysql/data# docker exec -it 670bef6847a1 bash
root@670bef6847a1:/# ls
bin  boot  dev	docker-entrypoint-initdb.d  entrypoint.sh  etc	home  lib  lib64  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
root@670bef6847a1:/# whereis mysql
mysql: /usr/bin/mysql /usr/lib/mysql /etc/mysql /usr/share/mysql
```

### 将docker容器中的文件拷贝到宿主机中
> 使用 `docker cp` 命令  

- 假如我们想要把mysql的配置文件拿出来，放到宿主机中，然后当创建其他的mysql的时候，可以把这个配置文件挂载到上面去  
```
docker cp mysql:/etc/mysql .
# 使用docker cp 命令 mysql:/etc/mysql 表示拷贝mysql容器中/etc/mysql目录到 . 即当前目录下
```
### 查看所有数据卷
```
docker volume ls
```

### docker 查看容器的日志信息
假如现在部署了一个项目，然后我们需要去查看`tomcat`的日志信息，因为是使用docker来部署，因此我们查看日志的信息，是指去查看容器的日志信息，可以使用 `docker logs [容器名|id]` 来查看容器的日志信息，如果想实时的查看信息，还可使用`docker logs -f [容器名|id]`的方式就会一直卡在日志，实时查看  

## Docker Compose 
`Docker Compose` 是 `Docker` 官方编排（`Orchestration`）项目之一，负责快速的部署==分布式应用==   
`Compose` 项目是 `Docker` 官方的开源项目，负责实现对 `Docker` 容器集群的快速编排。从功能上看，跟 `OpenStack` 中的 `Heat` 十分类似。  
`Compose` 定位是 「定义和运行多个 `Docker` 容器的应用（`Defining and running multi-container Docker applications`）」  
通过第一部分中的介绍，我们知道使用一个 `Dockerfile` 模板文件，可以让用户很方便的定义一个单独的应用容器。然而，在日常工作中，经常会碰到需要多个容器相互配合来完成某项任务的情况。例如要实现一个 `Web` 项目，除了 `Web` 服务容器本身，往往还需要再加上后端的数据库服务容器，甚至还包括负载均衡容器等。  

`Compose` 恰好满足了这样的需求。它允许用户通过一个单独的 `docker-compose.yml` 模板文件（`YAML` 格式）来定义一组相关联的应用容器为一个项目（`project`）。   
> `Compose` 中有两个重要的概念：   

- 服务 (`service`)：一个应用的容器，实际上可以包括若干运行相同镜像的容器实例。  
- 项目 (`project`)：由一组关联的应用容器组成的一个完整业务单元，在 `docker-compose.yml` 文件中定义。  
> Compose 的默认管理对象是项目，通过子命令对项目中的一组容器进行便捷地生命周期管理。  

### Docker Compose 安装与卸载
- 二进制包安装  
在 Linux 上的也安装十分简单，从 官方 [GitHub Release](https://github.com/docker/compose/releases) 处直接下载编译好的二进制文件即可。

例如，在 Linux 64 位系统上直接下载对应的二进制包。
```
$ sudo curl -L https://github.com/docker/compose/releases/download/1.17.1/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose
# 需要把上面的版本号改成官方的网址的版本号  
# 上面的命令将Docker Compose 下载到/usr/local/bin/的目录下,下载后可以查看
root@ubuntu:/# cd /usr/local/bin
root@ubuntu:/usr/local/bin# ll
total 17184
drwxr-xr-x  2 root root     4096 May  5 18:50 ./
drwxr-xr-x 11 root root     4096 May  5 13:51 ../
-rw-r--r--  1 root root 17586312 May  5 18:50 docker-compose
# 发现这个文件不具有执行的权限，因此需要修改权限  
$ sudo chmod +x /usr/local/bin/docker-compose  
# 然后查看版本信息
root@ubuntu:/usr/local/bin# docker-compose version
docker-compose version 1.25.5, build 8a1c60f6
docker-py version: 4.1.0
CPython version: 3.7.5
OpenSSL version: OpenSSL 1.1.0l  10 Sep 2019
```
- 卸载
如果是二进制包方式安装的，删除二进制文件即可。  
```
$ sudo rm /usr/local/bin/docker-compose
```

### Docker Compose 使用
场景：最常见的项目是 web 网站，该项目应该包含 web 应用和缓存。  
> 使用 `Docker Compose` 启动一个`tomcat`   

- 编写 `docker-compose.yml` 文件
```
version: '3'
services:
  tomcat:
    restart: always
    image: mytomcat
    container_name: tomcat
    ports:
      - 8080:8080

# 参数说明：
# version指定当前docker-compose的版本号，不能随便指定，目前是3版本 
# services指定这个项目需要部署的服务(镜像)  
# 因为我们需要撬动一个tomcat，所以里面的一个服务就是tomcat,这个名称可以自己随便起，代表服务名称  
# image：指定需要的镜像的名称
# container_name：为这个镜像起一个名字
# ports：映射的端口号，前面是宿主机，后面是当前这个tomcat服务的端口  
```
- 启动`docker-compose`
在`docker-compose.yml` 文件所在目录下执行  
```
root@ubuntu:/usr/local/docker/tomcat# docker-compose up
Creating network "tomcat_default" with the default driver
Creating tomcat ... done
Attaching to tomcat
tomcat    | NOTE: Picked up JDK_JAVA_OPTIONS:  --add-opens=java.base/java.lang=ALL-UNNAMED --add-opens=java.base/java.io=ALL-UNNAMED --add-opens=java.rmi/sun.rmi.transport=ALL-UNNAMED
tomcat    | 05-May-2020 11:25:15.855 INFO [main] org.apache.catalina.startup.VersionLoggerListener.log Server version name:   Apache Tomcat/9.0.34
tomcat    | 05-May-2020 11:25:15.869 INFO [main] org.apache.catalina.startup.VersionLoggerListener.log Server built:          Apr 3 2020 12:02:52 UTC
tomcat    | 05-May-2020 11:25:15.870 INFO [main] org.apache.catalina.startup.VersionLoggerListener.log Server version number: 9.0.34.0
```
- 测试
```
root@ubuntu:~# docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS                    NAMES
92d76bc16476        mytomcat            "catalina.sh run"   25 seconds ago      Up 23 seconds       0.0.0.0:8080->8080/tcp   tomcat
# 然后在浏览器访问
```
- 删除
```
root@ubuntu:/usr/local/docker/tomcat# docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS                    NAMES
92d76bc16476        mytomcat            "catalina.sh run"   25 seconds ago      Up 23 seconds       0.0.0.0:8080->8080/tcp   tomcat
root@ubuntu:/usr/local/docker/tomcat# docker-compose down 
Removing network tomcat_default
root@ubuntu:/usr/local/docker/tomcat#  
```
==【注意】==：所有的 `docker-compose` 必须在`docker-compose.yml` 文件所在的目录下执行，否则会报以下的错误
```
root@ubuntu:/usr/local/docker# docker-compose down
ERROR: 
        Can't find a suitable configuration file in this directory or any
        parent. Are you in the right directory?

        Supported filenames: docker-compose.yml, docker-compose.yaml
```
其他的操作命名和`docker`命令一样，最大的区别就是配置文件的区别  

> 再部署一个项目，项目里面启动2个服务：  
> 一个tomcat，ROOT目录下使用数据卷方式，里面就放一个index.html  
> 另一个就是mysql数据库,同时数据库也是使用数据卷的方式配置  

- 在`/usr/local/docker`目录下新建一个ROOT的目录，里面就放一个`index.html`文件，用于网站的文件信息  
- 在`/usr/local/docker`的目录下单独创建一个`myweb`的文件夹，专门用于构建这个项目  
- 在这个`myweb`文件中新建一个`docker-compose.yml`文件，并编写内容
```
version: '3'
services:
  # 启动一个web服务，放的镜像是一个tomcat
  web:
    restart: always
    image: mytomcat
    container_name: web
    ports:
      - 8080:8080
    # 下面讲数据卷挂载到tomcat的ROOT的目录上
    volumes:
      - /usr/local/docker/ROOT:/usr/local/tomcat/webapps/ROOT

  # 启动一个mysql的服务
  mysql:
    restart: always
    image: mysql:5.7.22
    container_name: mysql
    ports:
      - 3306:3306
    environment:
      TZ: Asia/Shanghai
      MYSQL_ROOT_PASSWORD: 123456
    # 执行mysql的命令
    command:
      --character-set-server=utf8mb4
      --collation-server=utf8mb4_general_ci
      --explicit_defaults_for_timestamp=true
      --lower_case_table_names=1
      --max_allowed_packet=128M
      --sql-mode="STRICT_TRANS_TABLES,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION,NO_ZERO_DATE,NO_ZERO_IN_DATE,ERROR_FOR_DIVISION_BY_ZERO"
    # 挂载mysql的数据卷，这里采用另一种写法来挂载数据卷，下面的my-data就相当于一个变量名，具体的内容在下面
    volumes:
      - mysql-data:/var/lib/mysql

# 定义一些通用的数据卷
volumes:
  mysql-data:
  # 没有值就用默认的
```
- 开始构建
```
root@ubuntu:/usr/local/docker/myweb# docker-compose up
Creating network "myweb_default" with the default driver
Creating volume "myweb_mysql-data" with default driver
Creating web   ... done
Creating mysql ... done
```
- 查看
```
root@ubuntu:/usr/local/docker# docker ps 
CONTAINER ID        IMAGE               COMMAND                  CREATED              STATUS              PORTS                    NAMES
a01fdfed3965        mytomcat            "catalina.sh run"        About a minute ago   Up About a minute   0.0.0.0:8080->8080/tcp   web
018ca5d38e71        mysql:5.7.22        "docker-entrypoint.s…"   About a minute ago   Up About a minute   0.0.0.0:3306->3306/tcp   mysql
```
发现构建成功，并且可以访问  
- 疑问：上面的tomcat目录，我们是自己指定ROOT的数据卷位置，我们只需要把我们的项目放到对应宿主机的目录下即可，但数据库呢？我们没有指定数据卷的位置，那么`docker-compose`放数据卷的默认位置在哪里呢？  
```
# 首先数据肯定是放在 /var的目录下，因此我们进入到 /var/lib的目录下
root@ubuntu:/var# cd /var/lib
root@ubuntu:/var/lib# ll
total 148
.....
drwxr-xr-x  2 root root    4096 Apr 30 23:54 dbus/
drwxr-xr-x  2 root root    4096 Apr 30 23:58 dhcp/
drwx--x--x 14 root root    4096 May  5 13:46 docker/
.....
# 发现里面有一个docker目录，进入到docker目录
root@ubuntu:/var/lib# cd docker
root@ubuntu:/var/lib/docker# ll
total 56
drwx--x--x 14 root root 4096 May  5 13:46 ./
drwxr-xr-x 38 root root 4096 May  3 18:26 ../
drwx------  2 root root 4096 May  2 19:05 builder/
drwx--x--x  4 root root 4096 May  2 19:05 buildkit/
drwx------  4 root root 4096 May  5 22:07 containers/
drwx------  3 root root 4096 May  2 19:05 image/
drwxr-x---  3 root root 4096 May  2 19:05 network/
drwx------ 42 root root 4096 May  5 22:07 overlay2/
drwx------  4 root root 4096 May  2 19:05 plugins/
drwx------  2 root root 4096 May  5 13:46 runtimes/
drwx------  2 root root 4096 May  2 19:05 swarm/
drwx------  2 root root 4096 May  5 14:53 tmp/
drwx------  2 root root 4096 May  2 19:05 trust/
drwx------  3 root root 4096 May  5 22:07 volumes/
# 发现这个文件夹里面都是放的docker的默认数据，进入到volumes文件夹
root@ubuntu:/var/lib/docker# cd volumes/
root@ubuntu:/var/lib/docker/volumes# ll
total 36
drwx------  3 root root  4096 May  5 22:07 ./
drwx--x--x 14 root root  4096 May  5 13:46 ../
-rw-------  1 root root 32768 May  5 22:07 metadata.db
drwxr-xr-x  3 root root  4096 May  5 22:07 myweb_mysql-data/
# 就发现了myweb_mysql-data的数据卷，进入
root@ubuntu:/var/lib/docker/volumes# cd myweb_mysql-data/
root@ubuntu:/var/lib/docker/volumes/myweb_mysql-data# ll
total 12
drwxr-xr-x 3 root root   4096 May  5 22:07 ./
drwx------ 3 root root   4096 May  5 22:07 ../
drwxr-xr-x 5  999 docker 4096 May  5 22:07 _data/
root@ubuntu:/var/lib/docker/volumes/myweb_mysql-data# cd _data
root@ubuntu:/var/lib/docker/volumes/myweb_mysql-data/_data# ll
total 188484
drwxr-xr-x 5  999 docker     4096 May  5 22:07 ./
drwxr-xr-x 3 root root       4096 May  5 22:07 ../
-rw-r----- 1  999 docker       56 May  5 22:07 auto.cnf
-rw------- 1  999 docker     1679 May  5 22:07 ca-key.pem
-rw-r--r-- 1  999 docker     1107 May  5 22:07 ca.pem
-rw-r--r-- 1  999 docker     1107 May  5 22:07 client-cert.pem
-rw------- 1  999 docker     1675 May  5 22:07 client-key.pem
-rw-r----- 1  999 docker     1341 May  5 22:07 ib_buffer_pool
-rw-r----- 1  999 docker 79691776 May  5 22:07 ibdata1
-rw-r----- 1  999 docker 50331648 May  5 22:07 ib_logfile0
-rw-r----- 1  999 docker 50331648 May  5 22:07 ib_logfile1
-rw-r----- 1  999 docker 12582912 May  5 22:07 ibtmp1
drwxr-x--- 2  999 docker     4096 May  5 22:07 mysql/
drwxr-x--- 2  999 docker     4096 May  5 22:07 performance_schema/
-rw------- 1  999 docker     1679 May  5 22:07 private_key.pem
-rw-r--r-- 1  999 docker      451 May  5 22:07 public_key.pem
-rw-r--r-- 1  999 docker     1107 May  5 22:07 server-cert.pem
-rw------- 1  999 docker     1679 May  5 22:07 server-key.pem
drwxr-x--- 2  999 docker    12288 May  5 22:07 sys/
# 这就找到了所有的数据库文件
# 同时我们还可以查看所有docker-compose默认的数据卷
root@ubuntu:/var/lib/docker/volumes/myweb_mysql-data/_data# docker volume ls
DRIVER              VOLUME NAME
local               myweb_mysql-data
```
- 停止项目
```
root@ubuntu:/usr/local/docker/myweb# docker-compose down 
Removing web   ... done
Removing mysql ... done
Removing network myweb_default
```
- `mysql8` 版本的配置
```
db:
    image: mysql
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: 123456
    command:
      --default-authentication-plugin=mysql_native_password
      --character-set-server=utf8mb4
      --collation-server=utf8mb4_general_ci
      --explicit_defaults_for_timestamp=true
      --lower_case_table_names=1
    ports:
      - 3306:3306
    volumes:
      - ./data:/var/lib/mysql
```

### Docker Compose 常用命令总结
- 前台运行
```
docker-compose up
```
- 后台运行
```
docker-compose up -d
```
- 启动
```
docker-compose start
```
- 停止
```
docker-compose stop
```
- 停止并移除容器
```
docker-compose down
```
# Docker

- 是一个开源应用容器引擎
- 打包应用以及依赖到一个可移植的容器中,移植到Linux 实现虚拟化技术
- 容器是互相之间不会有任何接口

## 应用场景

- Web应用的自动化打包和发布
- 自动化测试和持续集成,发布
- 在服务型环境中部署和调整数据库或其它的后台应用

## 优点

- 简化程序

- 节省开支

## Docker 架构

- Docker 使用客户端-服务器 (C/S) 架构模式，使用远程API来管理和创建Docker容器。
- Docker 容器通过 Docker 镜像来创建。
- 容器与镜像的关系类似于面向对象编程中的对象与类

![img](https://i.imgur.com/20nP9oZ.png)

## 安装 Docker(CentOS 7)

1. 确保liunx内核在3.10并且为64位
    - `uname -r`
        - 查看内核

2. `yum -y install docker`
    - 等待安装完成(确保有网络)
    - 安装目录为`/var/lib/docker/`

3. `service docker start`
    - 启动docker容器

## 相关连接

[Docker 官网](https://github.com/docker/docker)
[Docer 源码](https://github.com/docker/docker)

## 镜像操作(常用指令)

## 远程

- `docker login [选项] [目标地址]`
  - 默认的登陆官方仓库
    - -u
      - 登陆的用户名
    - -p
      - 登陆的密码

- `docker pull [选项] 镜像名[:版本号]`
  - -a
    - 拉取所有 tagged 镜像
  - --disable-content-trust
    - 忽略镜像的校验,默认开启

- `docker push [选项] 镜像名[:版本号]`
  - 上传到已登录的镜像仓库(需要先登录)
    - -disable-content-trust
      - 忽略镜像的校验,默认开启

- `docker search [OPTIONS] 镜像名`
  - --automated
    - 只列出 automated build类型的镜像
  - --no-trunc
    - 显示完整的镜像描述
  - -s
    - 列出收藏数不小于指定值的镜像

## 本地

- `docker images [选项] 镜像名[:版本号]`
- 查看本地镜像
  - -a
    - 列出本地所有的镜像（含中间映像层，默认情况下，过滤掉中间映像层）；
  - --digests
    - 显示镜像的摘要信息
  - -f
    - 显示满足条件的镜像
  - --format
    - 指定返回值的模板文件
  - --no-trunc
    - 显示完整的镜像信息
  - -q
    - 只显示镜像ID

- `docker rmi [OPTIONS] 镜像名[:版本号] [镜像名[:版本号]...]`
  - -f
    - 强制删除
  - --no-prune
    - 不移除该镜像的过程镜像，默认移除

- `docker build -t 镜像名[:版本号] path(生成目录)`
  - 创建镜像

- `docker save [选项] 镜像名[:版本号]`
- 输出镜像文件为.tar文件
  - -o
    - 输出到的文件

- `docker load -i .tar文件地址`
  - 本地导入镜像

- `docker import [选项] 文件名|文件地址| [[镜像名[:版本号]]`
- 本地导入
  - -c
    - 应用docker指令创建镜像
  - -m
    - 提交时的说明文字

## 容器操作(常用指令)

- `docker ps [选项]`
  - 显示容器
    - -a
      - 显示所有的容器，包括未运行的
    - -f
      - 根据条件过滤显示的内容
    - --format
      - 定返回值的模板文件
    - -l
      - 显示最近创建的容器
    - -n
      - 列出最近创建的n个容器
    - --no-trunc
      - 不截断输出
    - -q
      - 静默模式，只显示容器编号。
    - -s
      - 显示总的文件大小

- `docker run [选项] IMAGE [COMMAND] [ARG...]`
  - 创建容器并运行
    - --name
      - 为容器指定一个名称
    - -d
      - 后台运行容器，并返回容器ID
    - -p
      - liunx端口:容器端口

- `docker start [容器名[:版本号]`
  - 启动一个或多少已经被停止的容器

- `docker stop [容器名[:版本号]`
  - 停止一个运行中的容器
- `docker restart [容器名[:版本号]`
  - 重启容器

- `docker kill [选项] 容器名`
  - 杀死容器
    - -s :向容器发送一个信号

- `docker rm [选项] 容器名`
  - -f
    - 通过SIGKILL信号强制删除一个运行中的容器
  - -l
    - 移除容器间的网络连接，而非容器本身
  - -v
    - 删除与容器关联的卷

- `docker pause xxx`
  - 暂停容器中所有的进程。
- `docker unpause xx`
  - 恢复容器中所有的进程

- `docker create [选项] IMAGE [COMMAND] [ARG...]`
  - 创建容器不运行

- `docker exec [选项] CONTAINER COMMAND [ARG...]`
  - 进入运行容器中
    - -d
      - 分离模式: 在后台运行
    - -i
      - 即使没有附加也保持STDIN 打开
    - -t
      - 分配一个伪终端
    - `docker exec -it 容器id bash`
      - 进入运行容器

## 国内Docker镜像仓库

- [DaoCloud](https://account.daocloud.io)
- [阿里云](https://dev.aliyun.com/search.html)
- [希云](http://csphere.cn/hub/)
- [时速云](https://hub.tenxcloud.com/)
- [灵雀云](https://hub.alauda.cn/)
- [网易蜂巢](https://c.163.com/)

## Dockerfile

- Docker镜像通常是通过Dockerfile来创建的
- Dockerfile提供了镜像内容的定制
- 体现了层级关系的建立

## 常用编辑指令

|指令|说明|
|:----:|:----:|
|FROM|制定基础镜像来源|
|ADD|复制文件,并自动解压|
|ENV|设置环境便利|
|EXPOSE|指定对外暴露的端口|
|RUN|运行sh命令|
|CMD|执行exec命令,一个Dockerfile只能一个|

- jdk 配置写法
  - 解压目录必须为`/usr/local/src`

```Dockerfile
FROM index.alauda.cn/tutum/centos:6.5
#install jdk1.8
ADD jdk-8u51-linux-x64.tar.gz /usr/local/src
ENV JAVA_HOME=/usr/local/src/jdk1.8.0_51
ENV PATH=$JAVA_HOME/bin:$PATH
ENV CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
```

- tomcat配置写法

```Dockerfile
FROM jt-centos-jdk:0.0.1
#install jdk1.8
ADD jdk-8u51-linux-x64.tar.gz /usr/local/src
ENV JAVA_HOME=/usr/local/src/jdk1.8.0_51
ENV PATH=$JAVA_HOME/bin:$PATH
ENV CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
#install tomcat7
ADD apache-tomcat-7.0.55.tar.gz /usr/local/src
ENV CATALINA_HOME /usr/local/src/apache-tomcat-7.0.55
ENV PATH=$PATH:$CATALINA_HOME/bin
#把tomcat的8080端口暴露出去
EXPOSE 8080
CMD ["/usr/local/src/apache-tomcat-7.0.55/bin/catalina.sh","run"]
```

## docker镜像加速

- 阿里云镜像加速服务
  - [地址](https://cr.console.aliyun.com/cn-beijing/mirrors)

- 修改文件

```shell
vim /etc/docker/daemon.json <<-'EOF'
```

```json
{
  "registry-mirrors": ["加速地址"]
}
```

- 重启

```shell
#重启
sudo systemctl daemon-reload
sudo systemctl restart docker
```

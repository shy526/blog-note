# 环境搭建
- 阿里云服务器折腾
## nginx 安装
- 下载安装nginx依赖包
  - `yum -y install make zlib zlib-devel gcc-c++ libtool  openssl openssl-devel`
  - `yum install pcre`
  - `yum install pcre-devel`
>  `nginx`不支持`pcre2`
- 安装nginx
    1. `tar -xvf nginx-x.x.x.tar.gz`
    2. `cd nginx-x.x.x`
    3. `./configure `
    4. `make`
    5. `make install`
- 测试 `nginx`
  - `wget`
    - 测试即可

- nginx 部分指令
  -  开启
    - `nginx -c /usr/local/nginx/conf/nginx.conf`
      - -c指定配置文件
  - 重启
    - `nginx -s reload`
- 设置开机启动
  - [官方nginx提供的脚本](https://www.nginx.com/resources/wiki/start/topics/examples/redhatnginxinit/)
  - `vim /etc/init.d/nginx`
    - 创建文本,并黏贴文本
  - 修改脚本
    - `nginx={nginx-root}/nginx`
      - nginx 根目录
    - `NGINX_CONF_FILE={nginx-config}`
      - nginxConfig目录
    - `chkconfig --add /etc/init.d/nginx`
    - `chmod a+x /etc/init.d/nginx`
    - `chkconfig on`
  - 命令如下
    - `service nginx start`
      - 启动
    - `service nginx stop`
      - 停止
    - `service nginx reload`
      - 重新加载
- `systemctl` 启动配置
  - `vim /lib/systemd/system/nginx.service`
     - 输入如下内容
  ```config
  [Unit]
  Description=nginx
  After=network.target
    
  [Service]
  Type=forking
  ExecStart=/usr/local/nginx/sbin/nginx
  ExecReload=/usr/local/nginx/sbin/nginx -s reload
  ExecStop=/usr/local/nginx/sbin/nginx -s quit
  PrivateTmp=true
    
  [Install]
  WantedBy=multi-user.target
  ```
  - `systemctl enable nginx.service`
    - 设置开机启动
  - 命令
    - `systemctl start nginx.service`
      - 启动
    - `systemctl stop nginx.service　`
      - 停止
    - `systemctl restart nginx.service　`
      - 重启
    - `systemctl status nginx.service`
      - 查看状态 




## mysql 安装

- 根据mysql官方文档安装即可

- 遇到的错误
  - 缺少依赖库
    - yum 安装缺少的依赖库即可
  - Can't connect to local MySQL server through socket '/tmp/mysql.sock' (2)
    - `ln -s /var/lib/mysql/mysql.sock /tmp/mysql.sock`
      - 建立连接
- 修改初始密码
  - `update user set password=password("密码") where user='root'`;

- 设置开机自启
    1.  `chmod +x /etc/init.d/mysql`
    2. `chkconfig --add mysq`
    3. `chkconfig --add mysql`
    4. `chkconfig --list`
      - 查看是 3,4,5是否为on
        - 不为on时执行`chkconfig --level 345 mysql on`

- 开启远程访问权限
  - 设置安全规则开放3306端口
  - 进入mysql客户端
      1. `use mysql;`
      2. `update user set host = '%' where user = 'root';`
        - `%`所有ip都可以访问
      3. `select host, user from user`
        - 查看修改
      4. `FLUSH PRIVILEGES `
        - 启用
### 压缩版安装
  - 配置my.conf
  - 建立目录和空文件
  - `yum update`   !i6tyKv.4uU/
  - `yum install libaio`
    - 安装依赖
  - `yum install glibc.i686`
    - 修复32位程序在64位系统中执行
    - 可选
  - `./bin/mysqld --initialize --user=mysql`
    - 初始化数据库
  - `cp ./support-files/mysql.server /etc/init.d/mysql`
    - 修改mysql文件的目录`basedir`,`datadir`
  > 移动到/usr/local下避免不必要的错误
## git安装
- https://github.com/git/git/archive/v2.16.1.tar.gz 下载解压
  - `wget https://github.com/git/git/archive/v2.16.1.tar.gz`
  - `tar -xvf xxx.gz`
- 安装依赖
  - `yum -y install curl-devel expat-devel gettext-devel openssl-devel zlib-devel gcc perl-ExtUtils-MakeMaker`

- 编译安装
  - `make prefix=/usr/local/git all`
    - 编译
  - `make prefix=/usr/local/git install`
    - 安装
- 检查版本
  - `git --version`
    - 版本不正常,修改版本
    - `vim /etc/profile`
      - 添加如下
        - `export PATH=/usr/local/git/bin:$PATH`
      - `source /etc/profile`
        - 生效

- 部分使用
  - `git clone https://github.com/sunjiaqing/blog.git`
    - 克github隆远程仓库

## maven 安装
-  https://archive.apache.org/dist/maven/maven-3/3.5.0/binaries/apache-maven-3.5.0-bin.tar.gz 下载解压

- 修改profile
  ```
  export MAVEN_HOME=/home/work/maven/apache-maven-3.5.0
  export PATH=$PATH:$MAVEN_HOME/bin
  ```
  - `source /etc/profile`

- 测试
  - `mvn -version`
  - 修改本地仓库地址




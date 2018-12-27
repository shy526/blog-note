# 环境搭建
- 阿里云服务器折腾
## nginx 安装
- 下载安装nginx依赖包
  - SSL功能需要openssl库
    - 下载地址：http://www.openssl.org/
      - 安装
         1. `cd 到根目录`
         2. `./config`
         3. `make`
         4. `make install`
  - gzip模块需要zlib库
    - 下载地址：http://www.zlib.net/
      - 安装
            1. `cd 到根目录`
            2. `./configure `
            3. `make`
            4. `make install`
  - rewrite模块需要pcre库
    - 下载地址：http://www.pcre.org/
    - 安装
          1. `cd 到根目录`
          2. `./configure `
          3. `make`
          4. `make install`
> 需要gcc 支持   安装`yum install -y gcc gcc-c++`
- 安装nginx
    1. `tar -xvf nginx-x.x.x.tar.gz`
    2. `cd nginx-x.x.x`
    3. `./configure --with-pcre=../pcre根目录/ --with-zlib=../zlib根目录/ --with-openssl=../openssl根目录/`
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




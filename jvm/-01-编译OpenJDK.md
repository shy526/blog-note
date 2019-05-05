# 编译OpenJDK

## 构建编译环境

- 下载[OpenJDK 7 update60](http://hg.openjdk.java.net/jdk7u/jdk7u60/jdk/archive/33c1eee28403.zip)

- 安装CYGWIN
  - 下载 [CYGWIN 3.0.6](http://www.cygwin.com/setup-x86_64.exe)
    - 需要的工具
    - ar.exe
    - make.exe
    - m4.exe
    - cpio.exe
    - gawk.exe
    - file.exe
    - zip.exe
    - unzip.exe
    - free.exe

- 配置ant
  - 下载[ant](http://mirrors.tuna.tsinghua.edu.cn/apache//ant/binaries/apache-ant-1.10.5-bin.zip)
  - 环境变量配置
    - `ANT_HOME    {ant的根目录}`
    - `Path        %ANT_HOME%\bin`
    - `ClassPath   %ANT_HOME%\lib`
  - 验证 `ant -version`

## 待续
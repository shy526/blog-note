# apache ant
- 是一个将软件编译、测试、部署等步骤联系在一起加以自动化的一个工具
    - 一个Java库和命令行工具，可以帮助构建软件
        - 大多用于Java环境中的软件开发

## 配置环境
- ANT_HOME
    - 配置到文件根目录
- path
    - `%ANT_HOME%\bin`

- classpath
    - `%ANT_HOME%\lib`

## 验证
- 运行cmd
    - `ant`
    - 显示如下

        ```
        Buildfile: build.xml does not exist!
        Build failed
        ```
    - `ant -version`
        - 显示版本
- 如上表示安装成功

## ivy
- 将ivy中的ivy-x.x.x.jar放入`%ANT_HOME%\lib`中

## idea中配置ivy
- 安装`IvyIDEA`插件
    - 重启有效

- 设置`IvyIDEA`
    - 设置setting地址
     ![](https://i.imgur.com/TUUBFFl.png)
    - 设置ivy.xml
     ![](https://i.imgur.com/RJwzTkI.png)
    - `IVY_HOME/src/example/hello-ivy`
        - 在该目录下运行ant

- 重新编译项目



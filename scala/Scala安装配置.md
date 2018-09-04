# Scala 安装配置
-  http://www.scala-lang.org/downloads
  - 底部下载安装即可
- 配置环境变量(window)
  - `SCALA_HOME`
    - 指向scala根目录
  - `Path`
    - `%SCALA_HOME%\bin;%SCALA_HOME%\jre\bin;`
  - `ClassPath`
    - `.;%SCALA_HOME%\bin;%SCALA_HOME%\lib\dt.jar;%SCALA_HOME%\lib\tools.jar.;`
- 测试
  - 打开`cmd`输入`scala`
  - `scala -vesion`
    - 显示正常即为配置成功

- idea 配置 scala 环境
  - `Settings`--> `plugins` --> `Browse repositories..`
    - 搜索`Scala` --> 安装并重启Idea
      - 确认`File Encodings`中 的字符集设置为`utf-8`
        - > 否则导致无法运行

-  测试 idea
    -  创建Scala项目
        -  选择正确的jkd和ScalaSdk
        -  创建 入口类

```scala
object HelloWorld {
 def main(args: Array[String]) {
    println("Hello, world!") // 输出 Hello World
 }
}
```


  - 运行右键 `Run 入口类名`
    - 查看输出结果是否正常

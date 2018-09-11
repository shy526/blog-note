# springboot 配置文件外置的问题 

- 运行目录如下
![](assets/2018-09-11-15-31-17.png)

1. 先在test目录下运行
- 运行目录: ![](assets/2018-09-11-15-31-44.png)
- 结果: ![](assets/2018-09-11-15-32-13.png)

    - 此处和网络中说的相同读取,规则如下 
        1. Jar包同级目录的config目录
        2. Jar包同级目录
        3. classPath(即resources目录)的config目录
        4. classpath目录
2. 在test外执行
- 运行目录: ![](assets/2018-09-11-15-38-04.png)
- 运行结果: ![](assets/2018-09-11-15-37-48.png)


- 结论 
 - 这个和你执行的目录有关 而非jar 的位置
    - 我是怎么发现这个问题的 
        - 是因为我用jenkins去部署才发现的
 - `liunx`下也是相同的这里为了方便直接在win中演示


## `-Dloader.path`
- 额外启动项
 - `java -Dloader.path="d:\Users\admin\Desktop\zzz\test\config\" -jar  .\test\blog-gem-api-1.0-SNAPSHOT.jar`
    - 这个Windows执行无效
    - 这个liunx执行 有效
        - jenkins执行是无效的 

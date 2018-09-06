# 提供文件服务
1. 如何访问文件
  - /{name}/{profile}/{label}/{path}
    - /{应用名}/{标签环境}/{版本号}/{文件名}
2. 使用占位符号
  - 通过占位符为不同环境赋予不同的值
    - `${xxx.xx}`
    - /{name}/{profile}/是指{name}/{profile}.yml,{name}/{profile}.proptees 从那个一个中读取占位符\
    - 在同一个文件中可以使用
    ```yml
    name: x
    #default
    ---  # 截断
    spring:
      # profiles 指定版本号
      profiles: dev
    name: x1

    ```

# 嵌入式服务
- `spring.cloud.config.server.prefix`
  - 更改url的访问方式
  - `spring.cloud.config.server.prefix=/config`
    - 改为访问`localhost:8888/config`
- `spring.cloud.config.server.bootstrap`
  - 表示从git中读取
  - 默认false
  - 作为嵌入式服务时,官方建议开启

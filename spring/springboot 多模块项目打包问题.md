# Springboot 多模块打包问题

- application顺序
  1. 当前目录下的/config子目录，
  2. 当前目录
  3. 一个classpath下的/config包
  4. classpath根路径（root）

>Spring-boot配置文件的加载,先在与jar同级下查找,如果没有就去同级的config下查找；如果再没有,就在jar包中去查找相应的配置文件,如果再没有,就去jar包中的config下去查找,同名覆盖

- 打包问题
- 公用模块被多处依赖时找不到class
- 解决办法如下
  1. 修改pom
      - pom.xml

          ```xml
          <build>
          <plugins>
              <plugin>
                  <groupId>org.springframework.boot</groupId>
                  <artifactId>spring-boot-maven-plugin</artifactId>
                  <configuration>
                    <!--被公共依赖的排除-->
                      <classifier>exec</classifier>
                  </configuration>
              </plugin>
              <plugin>
                  <groupId>org.apache.maven.plugins</groupId>
                  <artifactId>maven-compiler-plugin</artifactId>
                  <configuration>
                      <source>1.8</source>
                      <target>1.8</target>
                  </configuration>
              </plugin>
              <plugin>
                  <groupId>org.apache.maven.plugins</groupId>
                  <artifactId>maven-surefire-plugin</artifactId>
                  <version>2.19.1</version>
                  <configuration>
                    <!--默认关掉单元测试 -->
                      <skipTests>true</skipTests>
                  </configuration>
              </plugin>
          </plugins>

          </build>
        ```

  2. 不添加插件即可

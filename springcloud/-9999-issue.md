
# issue

## eureka client 闪退问题

解决方法：

- 如果用的版本是`SpringBoot 2.1.2`和`SpringCloud Greenwich.RELEASE`无法启动
- 如果是高版本无法启动时，需要在pom.xml中加入如下依赖：

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```

## spring 请求路径日志没有打印问题

查看源码后发下如下问题  
在springmvc 5.1.4中如下代码段被删除

```java
if (logger.isInfoEnabled()) {
    logger.info("Mapped \"" + mapping + "\" onto " + handlerMethod);
}
```

### 完整代码片段

- spring-web-mvc 5.1.4
  - `org.springframework.web.servlet.handler.AbstractHandlerMethodMapping.MappingRegistry#register`

```java
    public void register(T mapping, Object handler, Method method) {
        this.readWriteLock.writeLock().lock();
        try {
            HandlerMethod handlerMethod = createHandlerMethod(handler, method);
            assertUniqueMethodMapping(handlerMethod, mapping);
            this.mappingLookup.put(mapping, handlerMethod);

            List<String> directUrls = getDirectUrls(mapping);
            for (String url : directUrls) {
                this.urlLookup.add(url, mapping);
            }

            String name = null;
            if (getNamingStrategy() != null) {
                name = getNamingStrategy().getName(handlerMethod, mapping);
                addMappingName(name, handlerMethod);
            }

            CorsConfiguration corsConfig = initCorsConfiguration(handler, method, mapping);
            if (corsConfig != null) {
                this.corsLookup.put(handlerMethod, corsConfig);
            }

            this.registry.put(mapping, new MappingRegistration<>(mapping, handlerMethod, directUrls, name));
        } finally {
            this.readWriteLock.writeLock().unlock();
        }
    }
```

- spring-web-mvc 4.3.12
  - `org.springframework.web.servlet.handler.AbstractHandlerMethodMapping.MappingRegistry#register`

```java
    public void register(T mapping, Object handler, Method method) {
        this.readWriteLock.writeLock().lock();
        try {
            HandlerMethod handlerMethod = createHandlerMethod(handler, method);
            assertUniqueMethodMapping(handlerMethod, mapping);
            // 此处代码被删除了
            if (logger.isInfoEnabled()) {
                logger.info("Mapped \"" + mapping + "\" onto " + handlerMethod);
            }
            this.mappingLookup.put(mapping, handlerMethod);

            List<String> directUrls = getDirectUrls(mapping);
            for (String url : directUrls) {
                this.urlLookup.add(url, mapping);
            }

            String name = null;
            if (getNamingStrategy() != null) {
                name = getNamingStrategy().getName(handlerMethod, mapping);
                addMappingName(name, handlerMethod);
            }

            CorsConfiguration corsConfig = initCorsConfiguration(handler, method, mapping);
            if (corsConfig != null) {
                this.corsLookup.put(handlerMethod, corsConfig);
            }

            this.registry.put(mapping, new MappingRegistration<T>(mapping, handlerMethod, directUrls, name));
        } finally {
            this.readWriteLock.writeLock().unlock();
        }
    }

```

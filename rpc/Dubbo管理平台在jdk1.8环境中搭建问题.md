# Dubbo 管理平台问题
- zookeeper 配置完成
## 错误代码

```
Error creating bean with name 'uriBrokerService':
Cannot create inner bean '(inner bean)' of type [com.alibaba.citrus.service.uribroker.impl.URIBrokerServiceImpl$URIBrokerInfo] while setting bean property 'brokers' with key [0];
nested exception is org.springframework.beans.factory.BeanCreationException:
 Error creating bean with name '(inner bean)#25': Cannot create inner bean 'server' of type [com.alibaba.citrus.service.uribroker.uri.GenericURIBroker] while setting constructor argument; nested exception is org.springframework.beans.factory.BeanCreationException:
    Error creating bean with name 'server':
 Error setting property values; nested exception is org.springframework.beans.NotWritablePropertyException:
 Invalid property 'URIType' of bean class[com.alibaba.citrus.service.uribroker.uri.GenericURIBroker]:
Bean property 'URIType' is not writable or has an invalid setter method. Does the parameter type of the setter match the return type of the getter?
```

### 更改方法
1. 修改环境jdk
    - `JAVA_HOME`


2. 修改tomcat默认启动jdk
    - 在`%tomcat%/bin/setclasspath.bat`文件头添加
    - `set JAVA_HOME=C:\Program Files\Java\jdk1.7.0_67`
    - `set JRE_HOME=C:\Program Files\Java\jdk1.7.0_67\jre`


3. 修改项目依赖重新打包
    - http://blog.csdn.net/blue_dd/article/details/51298438


# jackson
- Jackson可以轻松的将Java对象转换成json对象和xml文档，同样也可以将json、xml转换成Java对象
- Jackson所依赖的jar包较少，简单易用并且性能也要相对高些

## 特点
- 简单易用
- 无需创建映射
- 性能高
- 不依赖
- 开源

## 三种处理 JSON方式
1. 流式API
    - 读取并将JSON内容写入作为离散事件
。JsonParser读取数据，而JsonGenerator写入数据。它是三者中最有效的方法，是最低的开销和最快的读/写操作。它类似于Stax解析器XML。

2. 树模型
    - 准备JSON文件在内存里以树形式表示
    - `ObjectMapper`构建`JsonNode`节点树
    - 这是最灵活的方法。它类似于XML的DOM解析器。

3. 数据绑定
    - 转换JSON并从POJO（普通Java对象）使用属性访问或使用注释
    - 它有两个类型

    - 简单的数据绑定
        - 转换JSON和Java Maps, Lists, Strings, Numbers, Booleans 和null 对象。

    - 全部数据绑定
        -  转换为JSON从任何JAVA类型。

## ObjectMapper
- ObjectMapper读/写JSON两种类型的数据绑定
- 数据绑定是最方便的方式是类似XML的JAXB解析器



# 简单的数据/全数据绑定
|JSON类型|Java类型|
|:-----:|:-----:|
|object |	LinkedHashMap<String,Object>|
|array |	ArrayList<Object>|
|string |	String|
|complete | number	Integer,Long,BigInteger|
|fractional | number Double,BigDecimal|
|true/false |	Boolean|
|null |	null|
- API
    - 转换为json
        - `public String writeValueAsString(Object value)`
            - 根据`get`方法生成json串
        - `public void writeValue(File resultFile, Object value)`
            - 序列化到莫个文件中
    - 转换为对象
        - `public <T> T readValue(JsonParser jp, Class<T> valueType)`
            - 具有多个重载方法


##  树模型
- 树模型准备JSON文件的内存树表示

- API
    -  `public JsonNode readTree(String content)`
        -  将JSON串转换为一个根节点
    - 便利JsonNode
        - `JsonNode.path(xxx)`
            - 选择莫个节点
        - `JsonNode.getTextValue()`
            - 获取单个值
            - 或者其它`get类型value()`
        - `JsonNode.getElements()`
            - 获取多个值

     -  `public <T> T treeToValue(TreeNode n, Class<T> valueType)`
         -  将树转换为Java对象

## 流式API
- 流式API读取和写入JSON内容离散事件
- JsonParser读取数据(写入JSON串)
- JsonGenerator写入数据(解析JSON串)
- 是最低开销和最快的读/写操作
- 类似于XML的Stax解析器。

### JsonParser
- `JsonFactory.createJsonParser()`
    - 创建JsonParser
- `nextToken()`
    - 读取每个JSON字符串作为标记
        - `JsonToken.END_OBJECT`
            - 结束标记
-   `getCurrentName()`
    -  获取key

- `nextToken()`
    - `JsonToken.END_ARRAY`
        - 数组结束标记
    - 移动到下一个

- `getxxx`取值

    - 取value

### JsonGenerator
- `JsonFactory.createJsonGenerator()`
    - 获取`JsonGenerator`

- `write***()`
    - 方法来写每一个JSON值

## json转换为复杂java对象
```
objectMapper.readValue(objectMapper.readTree(JSON串).traverse(),objectMapper.
						getTypeFactory().
                    constructCollectionType(List.class, 范型.class));
```



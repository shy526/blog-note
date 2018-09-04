# mapRecduce

## MRunit

```xml
<dependency>
    <groupId>org.apache.mrunit</groupId>
    <artifactId>mrunit</artifactId>
    <version>1.1.0</version>
    <classifier>hadoop2</classifier>
    <!--不注释掉 导致无法导包--->
    <!--<scope>test</scope>-->
</dependency>
<dependency>
```

```java
@Test
public void proocessesValidRecord() throws IOException {
    Text value=new Text("0096007026999992016062218244+00000+000000FM-15+702699999V0209999C000019999999N999999999+03401+01801999999ADDMA1101731999999REMMET069MOBOB0 METAR 7026 //008 000000 221824Z AUTO 00000KT //// 34/18 A3004=");
    MapDriver<LongWritable,Text,Text,IntWritable> test = new MapDriver<>();
    //设置map
    MapDriver<LongWritable, Text, Text, IntWritable> test2 = test.withMapper(( new MaxTemperatureMapper()));
    //输入
    MapDriver<LongWritable, Text, Text, IntWritable> test3 = test2.withInput(new LongWritable(2), value);
    //输出标胶
    List<Pair<Text, IntWritable>> run = test3.withOutput(new Text("2016"), new IntWritable(340)).runTest(); //runTest 进行测试
    System.out.println("run = " + run);

    }
```

## 拆解map
- 解析部分重构为parser类,对parser类进行单元测试即可

## 本地运行测试数据 
-     - 需要继承 configured 实现Tool
- `hadoop jar xx.jar -conf 配置文件`
    - `mapreduce.framework.name`要求被设置为`local`
- `hadoop jar xx.jar -fs file:///input -jt xxxoutput `
    - 使用选项来进行

## 测试驱动程序
- 利用Tool,Configuration类进行编写测试程序
    - 需要继承 configured 实现Tool

```java
  /* 测试驱动
   * @param tool 驱动类
   * @param input 输入文件夹
   * @param output 输出文件夹
   * @return 返回 exitcode
   */
public int  test3(Tool tool,String input,String output){
    Configuration  conf=new Configuration();
    conf.set("fs.defaultFS","file:///");
    conf.set("mapreduce.framework.name","local");
    conf.setInt("mapreduce.task.io.sort.mb",1);
    Path inputPath=new Path(input);
    Path outputPath=new Path(output);
    try {
        FileSystem fs=FileSystem.getLocal(conf);
        //删除输出
        fs.delete(outputPath,true);
        Tool tool1 = tool.getClass().newInstance();
        return tool1.run(new String[]{inputPath.toString(), outputPath.toString()});
    } catch (Exception e) {
        e.printStackTrace();
    }
    return -1;
}


```


## mini集群测试
- 百度吧 太烦了还不如上环境

## hadoop web 界面
- http://resource-manager-host:8088
- 浏览作业信息,跟踪作业运行进度,查找完成统计信息和日志


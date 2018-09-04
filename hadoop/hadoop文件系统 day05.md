# hadoop 文件系统
- hadoop有一个抽象的文件系统概念
  - hdfs只是其中一个实现
  - jaba抽象类
    - `org.apache.hadoop.fs.FileSystem`
      - 定义文件系统中的客服端

|   文件系统    | URL方案  |                java实现                |                         描述                         |
|:-------------:|:--------:|:--------------------------------------:|:----------------------------------------------------:|
|     local     |   file   |           fs.LocalFileSystem           |                客户端校验本地磁盘系统                |
|     HDFS      |   hdfs   |       hdfs.DistributedFileSystem       |      hadoop的分布式文件访问系统,与MapReduce结合      |
|    WebHDFS    | Webhdfs  |       Hdfs.web.WebHdfsFileSystem       |                  基于HTTP的文件系统                  |
| SecureWebHDFS | swebhdfs |      hdfs.web.SwebHdfsFileSystem       |                  WebHDFS的HTTPS版本                  |
|      HAR      |   har    |            fs.HarFileSystem            |                用于文件存档的文件系统                |
|     View      |  viewfs  |         viewfs.ViewFileSystem          | 针对hadoop文件系统的挂载表(联邦namenode创建的挂载表) |
|      FTP      |   ftp    |          fs.ftp.FTPFileSystem          |              由FTP服务器支持的文件系统               |
|      S3       |   S3a    |          fs.s3a.S3AFileSystem          |               由AmazonS3支持的文件系统               |
|     Azure     |   wasb   |     fs.azure.NativeAzureFileSystem     |             MicrosoftAzure支持的文件系统             |
|     Swift     |  swift   | fs.swift.snative.SwiftNativeFileSystem |            OPenStack Swift支持的文件系统             |
- 所有java实现都在`org.apache.hadoop`包中

## 接口
1. http
    - 非java开发的应用访问HDFS会很方便
    - WebHDFS协议提供的HTTP REST API 则可以方便其他语言与HDFS交互
    - HTTP接口比原生的java客户端要慢
    - HTTP访问的两种方式
      1.  直接访问
          - HDFS守护进程直接服务于来自客户端的HTTP请求
          - 流程
            - http请求 --> namenode -重定向-> datenode(以流方式传输)
      2. 通过代理(一个或多个访问)
          - 使用DistributedFile API 访问HDFS
          - 可以使用更严格的防火墙策略和宽带限制
          - 在不同的数据中心部署的Hadoop集群之间数据传输
          - 默认监听端口14000

2.C语言
    - libhdfs的C语言库
      - 使用java原生接口实现
      - libwebhdfs库(https版)
    - 开发滞后于java接口

3. NFS
    - 使用Hadoop的NFSv3网关将HDFS挂载为本地客户端的文件系统

4. FUSE
    - 用户空间文件系统


# java

## url访问数据

- url 访问hdfs 数据
```java
import org.apache.hadoop.fs.FsUrlStreamHandlerFactory;
import org.apache.hadoop.io.IOUtils;
import java.io.IOException;
import java.io.InputStream;
import java.net.URL;
public class URLCat {
    static {
        /**
         * 每个java虚拟机只能调用一次该方法
         * 自定义扩展协议
         */
        URL.setURLStreamHandlerFactory(new FsUrlStreamHandlerFactory());
    }
    public static void main(String[] args) {
        InputStream in=null;
        try {
            in=new URL("hdfs://192.168.198.128/xx.txt").openStream();
            /**
             * 参数1:输入流
             * 参数2:输出流:
             * 参数3:缓存大小
             * 参数4:自动关流
             */
            IOUtils.copyBytes(in,System.out,4096,false);
            System.out.println("gg ");
        } catch (IOException e) {
            e.printStackTrace();
        }finally {
            //关流
            IOUtils.closeStream(in);
        }
    }
}
```
> 当设置为localhost时无法远程访问 需要修改/etc/hosts文件
>>设置`ip  localhost` ip不能为 127.0.0.1


- FileSystemAPI
  - 解决 `URL.setURLStreamHandlerFactory(new FsUrlStreamHandlerFactory());`只能运行一次的问题
  - 提供三个静态工厂类
    - `public static FileSystem get(ConfigUration conf) throws IOException`
      - `ConfigUration`:封装了客户端和服务器配置
      - 读取类路径下的`core-site.xml`
    - `public static FileSystem get(URI uri,ConfigUration conf) throws IOException`
      - uri和权限来确定要使用的文件系统
    - `public static FileSystem get(URI uri,ConfigUration conf,String user) throws IOException`
      - 给定用户来访问文件系统
  - `public static LocalFileSystem getLocal(ConfigUration conf)`
    - 可以方便的获得一个本地实例

- 读取数据
```java
import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.fs.FSDataInputStream;
import org.apache.hadoop.fs.FileSystem;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.IOUtils;
import org.junit.Test;
import java.io.IOException;
import java.net.URI;

public class FileSystemCat {
    private String uri="hdfs://192.168.198.128/xx.txt";
    @Test
    public void test1(){
        Configuration  config=new Configuration();
        /**
         * FSDataInputStream 支持随机访问
         * 类似 ReadomAccessFile类
         */
        FSDataInputStream in=null;
        try {
            FileSystem fs=FileSystem.get(URI.create(uri),config);
            in= fs.open(new Path(uri));
            IOUtils.copyBytes(in,System.out,4096,false);
            System.out.println("in.getPos() = " + in.getPos());
            //设置指针所在位置,超出最大值抛出ioExption
            in.seek(0);
            System.out.println("in.getPos() = " + in.getPos());
            IOUtils.copyBytes(in,System.out,4096,false);
            in.seek(10);
            //设置指针所在位置,不支持往前移动的操作,只支持往文件末尾移动
            in.skip(-2);
            System.out.println("in.getPos() = " + in.getPos());
            in.skip(2);
            System.out.println("in.getPos() = " + in.getPos());
            in.seek(37);
            //超出不会抛出异常
           in.skip(1);
            System.out.println("in.getPos() = " + in.getPos());
        } catch (IOException e) {
            e.printStackTrace();
        }finally {
            IOUtils.closeStream(in);
        }
    }
}
```

- 写入模式
  - `public FsDateOutputSteam create(Path f)`
    - 多个重载方法
      - 可以指定强制覆盖,文件备份数量,写入文件所需的缓冲区,文件块大小,文件权限
    - 写入时不存在父级目录会自动创建
  - Progressable接口的progressable方法
    - 每次一次写入datanode都会被调用
  - `public FsDateOutputSteam append(Path f)`
    - 追加模式
    - HDFS文件系统支持,S3文件系统不支持

- 写入列子
```java
  @Test
  public void test2(){
      String url= "hdfs://192.168.198.128/sun/xx1.txt";
      InputStream in=null;
      FSDataOutputStream out=null;
      try {
          in = new BufferedInputStream(new FileInputStream("E://test/sj.txt"));
          Configuration config=new Configuration();
          /**
           * 这里指定用户,否则会导致没有权限
           */
          FileSystem fs=FileSystem.get(URI.create(url),config,"root");
          //检测父目录是否存在
          if (fs.exists(new Path(url))){
              return;
          }
          out = fs.create(new Path(url), ()-> System.out.println("."));
          IOUtils.copyBytes(in,out,4096,false);
      } catch (Exception e) {
          e.printStackTrace();
      }finally {
          IOUtils.closeStream(in);
          IOUtils.closeStream(out);
      }
  }
```

- 追加列子
```java
@Test
public void test3(){
    String url= "hdfs://192.168.198.128/sun/xx1.txt";
    InputStream in=null;
    FSDataOutputStream out=null;
    try {
        Configuration config=new Configuration();
        //设置追加模式 可以不设置,配置文件也不需要修改
        //config.setBoolean("dfs.support.append", true);
        /**
         * datanode小于3 时关掉 防止写入失败
         * default在3个或以上备份的时候，是会尝试更换结点尝试写入datanode
         * 而在两个备份的时候，不更换datanode，直接开始写
         * 对于3个datanode的集群，只要一个节点没响应写入就会出问题
         */
       config.set("dfs.client.block.write.replace-datanode-on-failure.policy" ,
                "NEVER" );
      //失败时切换策略 默认true 可以不设置
    /*config.set( "dfs.client.block.write.replace-datanode-on-failure.enable" ,
                "true" );*/
                //设置备份数
      //config.set("dfs.replication", "1");
        FileSystem fs=FileSystem.get(URI.create(url),config,"root");
        out = fs.append(new Path(url));
        out.writeUTF("\n"+LocalDateTime.now().toString()+"\n");
        System.out.println("g = " );
    } catch (Exception e) {
        e.printStackTrace();
    }finally {
        IOUtils.closeStream(in);
        IOUtils.closeStream(out);
    }
}
```

- 创建目录

```java
@Test
public void mkdirsTest(){
    String url= "hdfs://192.168.198.128/sun1";
    Configuration config=new Configuration();
    try {
        //创建需要权限
        FileSystem fs = FileSystem.get(URI.create(url),config,"root");
        //与java.io.File 类似的mkdirs
        boolean mkdirs = fs.mkdirs(new Path(url));
        if (mkdirs){
            System.out.println("创建成功");
        }else{
            System.err.println("创建失败");
        }
    } catch (IOException e) {
        e.printStackTrace();
    } catch (InterruptedException e) {
        e.printStackTrace();
    }
}
```
> 调用create方法时会自动创建父级目录

- 文件元数据
  - 由FileStatus类封装
    - 文件长度,块的大小,复本,修改时间,所有者,权限信息
    - `FileSystem.getFileStatus`获取

```java
@Test
public void FileStatusTest(){
    String url="hdfs://192.168.198.128/";
    Configuration config=new Configuration();
    try {
        FileSystem fs=FileSystem.get(URI.create(url),config,"root");
        FileStatus fileStatus = fs.getFileStatus(new Path("/sun/xx1.txt"));
        System.out.println("status = " + fileStatus);
        System.out.println("所在路径 = " + fileStatus.getPath().toString());
        System.out.println("是否为目录 = " + fileStatus.isDirectory());
        System.out.println("文件长度 = " + fileStatus.getLen());
        System.out.println("修改时间 = " + fileStatus.getModificationTime());
        System.out.println("备份数 = " + fileStatus.getReplication());
        System.out.println("块大小 = " + fileStatus.getBlockSize());
        System.out.println("文件所有者 = " + fileStatus.getOwner());
        System.out.println("所属分组 = " + fileStatus.getGroup());
        System.out.println("权限 =" + fileStatus.getPermission().toString());
    } catch (IOException e) {
        e.printStackTrace();
    } catch (InterruptedException e) {
        e.printStackTrace();
    }
}
```

> 路径不存在时会抛出FileNotFoundException异常
>> 调用 FileSystem.exists 检查

- 列出所有文件

- 递归遍历所有文件
```java
@Test
public void listStatusTest(){
    String url="hdfs://192.168.198.128/";
    Configuration configuration=new Configuration();
    try {
        FileSystem fs= FileSystem.get(URI.create(url),configuration,"root");
        allFile(fs,new Path("/"));

    } catch (IOException e) {
        e.printStackTrace();
    } catch (InterruptedException e) {
        e.printStackTrace();
    }
}
public void allFile(FileSystem fs,Path path) throws IOException {
  //path为目录时返回0或多个,为文件时返回为1的FileStatus数组
    FileStatus[] fileStatuses = fs.listStatus(path);
    for (FileStatus f:fileStatuses){
        System.out.println("f.getPath().toString() = " + f.getPath().toString());
        if (f.isDirectory()){
            allFile(fs,f.getPath());
        }
    }
}
```

- PathFilter
  - 与`java.io.Filefileter`类似

```java
public void allFile(FileSystem fs,Path path) throws IOException {
       FileStatus[] fileStatuses = fs.listStatus(path, (path1)->{
           if (path1.toString().equals("hdfs://192.168.198.128/sun")||path1.getParent().toString().equals("hdfs://192.168.198.128/sun")){
               return  true;
           }
           return false;
       });
       for (FileStatus f:fileStatuses){
           System.out.println("f.getPath().toString() = " + f.getPath().toString());
           if (f.isDirectory()){
               allFile(fs,f.getPath());
           }
       }
   }
```
> FileUti.stat2Paths(FileStatus[] fs)将FileStatus数组转换为Path数组

- 通配符号
- `public FileStatus[] globStatus(Path pathPatten)`
  - 返回 Filestatus对象并安路径排序

| 通配符 |    名称    |                      匹配                       |
|:------:|:----------:|:-----------------------------------------------:|
|   *    |    星号    |                 匹配0到多个字符                 |
|   ?    |    问号    |                  匹配单个字符                   |
|  [ab]  |   字符类   |            匹配{a,b}集合中的一个字符            |
| [^ab]  |   非字符   |           匹配非{a,b}集合中的一个字符           |
| [a-b]  |  字符范围  |  匹配{a-b}范围内的字符包含啊a,b,a必须小于等于b  |
| [^a-b] | 非字符范围 | 匹配非{a-b}范围内的字符包含啊a,b,a必须小于等于b |
| {a,b}  |   或选择   |           匹配包含a或b中的一个表达式            |
|   \c   |  转义字符  |                    匹配字符c                    |

- 删除数据

```java
@Test
  public void deleteTest(){
      String url="hdfs://192.168.198.128/";
      Configuration configuration=new Configuration();
      try {
          FileSystem fs= FileSystem.get(URI.create(url),configuration,"root");
          //如path 代表的是个文件或者空目录 会忽略recursive的值
          //如果path代表一个非空目录,recursive的值为false时会抛出io异常
          //recursive为false时,只有文件和非空目录可以删除
          boolean delete = fs.delete(new Path(url + "sun1"), true);
          if (delete){
              System.out.println("删除成功");
          }else{
              System.out.println("删除失败");
          }
      } catch (IOException e) {
          e.printStackTrace();
      } catch (InterruptedException e) {
          e.printStackTrace();
      }
  }
```


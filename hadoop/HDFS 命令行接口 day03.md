# 命令行

- `hadoop fs -copyFromLocal 本地路径 [hdfs://ip/]hdfs路径`
  - 本地文件上传hdfs
  - 默认拼接`core-site.xml`中配置的`fs.defaultFS`

- `hadoop fs copyToLocal hdf路径 本地路径`
  - 下载hdfs上的文件

- `hadoop fs -ls`
  - 查看目录列表
  - 抛出异常
    - 2.7以上版本无法识别在那个目录 需要拼接 路径
  - `hadoop fs -ls /`
  - `hadoop fs -ls /.`
    - 查看文件权限

- `hadoop fs -mkdir /[路径]/文件`
  - 创建目录

- `hadoop fs  -getmerge hdfs文件夹 本地文件夹`
  - 合并hdfs文件夹中的文件
  - `sort文件 | tail`
    - 排序观看

## 命令详细

- [csdn](https://www.cnblogs.com/MrFee/p/4683953.html)
- [官网](http://hadoop.apache.org/docs/r2.9.0/hadoop-project-dist/hadoop-common/FileSystemShell.html
)

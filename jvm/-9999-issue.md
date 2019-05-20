# issue

## java gui不正常GC

程序最小化后工作内存被自动交换到磁盘的页面文件中,导致不正常gc

- 解决
  - 添加启动参数`-Dsun.awt.keepWorkingSetOnMinimize=true`
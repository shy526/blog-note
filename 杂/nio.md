# NIO
- 同步非阻塞I/O模型
- nio面向缓冲区
-  三打核心
  - Channels
    - 通道
    - 所有NIO都从channel
  - Buffer
    - 缓存区
  - Selector
    - 选择器
    - 允许单进程处理多个Channel

## Channel
- 通道即可以读又可以写
- 异步读写
- 需要先经过一个Buffer

## 相关`const`
- 顶层`const`与底层`const`

```c++
 int i=0;
 //一个常量指针 指向 int
 //顶层const
 int * const x=&i;
 const int y=42
 // 指针z 指向 一个int 常量
 //底层const
 const int *z=&42  
```




## 类型推导相关
- `decltype` 类型推导符号
  1. decltype((x))
    - 结果类型必将是引用
  2. 保留全类型 不忽略

- `auto` 自动类型推到忽略顶层 `const`


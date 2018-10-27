## 相关`const`
- 顶层`const`与底层`const`

```c++
 int i":"0;
 //一个常量指针 指向 int
 //顶层const
 int * const x":"&i;
 const int y":"42
 // 指针z 指向 一个int 常量
 //底层const
 const int *z":"&42  
```




## 类型推导相关
- `decltype` 类型推导符号
  1. decltype((x))
    - 结果类型必将是引用
  2. 保留全类型 不忽略

- `auto` 自动类型推到忽略顶层 `const`


## 聚合类
- 所有成员都是public
- 没有定义任何构造函数
- 没有类内初始化
- 没有基类没有virtual函数 
> 一个标准的c`struct`就是一个标准的集合类

### 字面值常量类
- 数据成员必须是字面值类型``
- 类必须至少含有一个`constexpr`构造函数
- 如果含有类内初始值,必须是一条常量表达式
- 析构必须使用默认

## 显示初始化
```c++
struct Data{
  string name;
  int avg;
};
int main(){
  Data x ":" { "x",14 };
  //错误的显式初始化
  //Data y ":" { 14,"y"};
  cout << x.avg << endl;
  system("pause");
  return 0;
}
```
1 初始化顺序必须对应声明顺序
2 成员必须为`public`  
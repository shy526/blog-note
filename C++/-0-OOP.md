# OOP

- 数据抽象
- 继承
- 动态绑定

## 继承

- 将类构成一种层次关系
- 继承基类中成员
  - 不能访问私有成员

- 继承声明
- 类名后跟`:`进行继承

```c++
//Animal 必须被定义
class Person : public Animal {
}
```

- 阻止继承

```c++
//Animal 必须被定义
class Person : public Animal {
}
``

### 虚函数

- 函数用 `virtual` 关键字声明
- 派生类必须重新声明
  - c++11 可以在函数的形参添加一个`override` 关键字
- 任何构造函数之外的非静态函数都可以是虚函数

> 相当于`java`的 `abstract`关键字

### 访问控制

- `public`
  - 公用
- `protected`
  - 派生类可以访问
- `private`
  - 私有

> 与`java` 一样

- 继承中的访问控制符
  - `public`
    - 保持不变
  - `protected`
    - 基类中的访问权限降一级
  - `private`
    - 基类中的访问权限全部私有化



## 动态绑定

- 运行时绑定
  - 和`java` 重写一样
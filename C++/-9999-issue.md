# 问题集

## 头文件互相引用

### 错误的列子

- A.h

```c++
#ifndef DEMO_A_H
#define DEMO_A_H

#include <string>
#include "B.h"

class A{
private:
public:
    A(const std::string &name) : name(name) {}

private:
    std::string name;
public:
    const std::string &getName() const {
        return name;
    }
    B buildB(){
        return B("B")
    }

};
#endif //DEMO_A_H
```

- B.h

```c++
#ifndef DEMO_B_H
#define DEMO_B_H

#include <string>
#include "A.h"
class B{
private:
public:
    B(const std::string &name) : name(name) {}

private:
    std::string name;
public:
    const std::string &getName() const {
        return name;
    }
    A buildA(){
        return A("A");
    }
};
#endif //DEMO_B_H
```

### 修改后

- B.h

```c++
#ifndef DEMO_B_H
#define DEMO_B_H

#include <string>
class A;
class B{
private:
public:
    B(const std::string &name) : name(name) {}


private:
    std::string name;
public:
    const std::string &getName() const {
        return name;
    }
    A buildA();
};
#endif //DEMO_B_H
```

- B.cpp

```c++
#include "B.h"
#include "A.h"
A B::buildA() {
    return  A("A");
}

```

- 统一测试用`main`

```c++
#include <iostream>
#include "B.h"
#include "A.h"
int main() {
    A a("xxx");
    auto ab=a.buildB();
    std::cout << ab.getName() << std::endl;

    B b("xxx");
    auto ba=b.buildA();

    std::cout << ba.getName() << std::endl;
    return 0;
}
```

> 严格安装规范写代码就能避免互相引用的问题,但是真确的做法是在另一个`class ?;`声明的类中应使用指针,而非一个类的实体,这是一种错误的设计
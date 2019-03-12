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
        return  new B("B")
    }

};
#endif //DEMO_A_H
```

- B.h

```c++
//
// Created by ccxh on 2019/3/12.
//

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
    A buildB(){
        return new A("A");
    }
};
#endif //DEMO_B_H
```
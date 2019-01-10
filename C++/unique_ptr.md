
# unique_ptr
- 莫个时刻只能有一个unique_ptr指向给定的对象,当unqiue_ptr销毁时所指对象也会被销毁
- 必须采用直接初始化
- 不支持拷贝和赋值
- 兼容`auto_ptr`


|                     |                              描述                              |
|:-------------------:|:--------------------------------------------------------------:|
|   `unique<T> u1`    |                  空unique 可以指向T类型的对象                  |
|  `unique<T,D> u2`   | 空unique 可以指向T类型的对象,u2会使用类型为D的可调用对象来释放 |
| `unique<T,D> u1(d)` |          空unique 可以指向T类型的对象,u2会使用d来释放          |
|     `u=nullptr`     |                        释放指针,将u置空                        |
|    `u.rekease()`    |            放弃指针控制权,返回内置指针并将自身置空             |
|     `u.reset()`     |                        释放并将自身置空                        |
|    `u.reset(q)`     |                   放弃指针控制权,并将u指向q                    |
| `u.reset(nullptrr)` |                                                                |

> `auto_ptr` 不能被保存在容器中,不能从函数中返回,拥有`unique_ptr`的部分功能

- 删除器示例

```c++
#include <iostream>
#include <memory>

void deleter(int * a){
    std::cout <<"删除器被调用"<< std::endl;
    delete  a;
}

int main() {

    std::unique_ptr<int, decltype(deleter)*> p(new int(10),deleter);
    std::cout <<*p << std::endl;
    return 0;
}
```

# weak_ptr
- 不控制所指向对象生命周期的智能指针
- 指向一个`shared_ptr`
- 不会计算引用数,是一种弱引用
-  没有重载`*`和`->`


|                     |                          描述                          |
|:-------------------:|:------------------------------------------------------:|
|   `weak_ptr<T> w`   |              weak_ptr 可以指向T类型的对象              |
| `weak_ptr<T> w(sp)` | 与 shared_ptr ps 指向相同的对象,所指对象的类型必须相同 |
|       `w = p`       |         p 可以是一个 `weak_ptr`或`shared_ptr`          |
|       `w.reset()`       |         置空         |
|       `w.use_count()`       |         共享对象shared_ptr的数量        |
|       `w.expired()`       |         `w.use_count()` 为0 返回true        |
|       `w.lock()`       |         `w.expired()` 为true 返回一个空shared_ptr,否则返回一整个指向w的对象的shared_ptr        |



- weak_ptr 特性的例子

```c++

void weakTest(){
    auto ptr=std::make_shared<int>(42);
    cout<<"ptr引用数:"<<ptr.use_count()<<endl;
    weak_ptr<int> wPtr(ptr);
    cout<<"设置weak_ptr后:"<<ptr.use_count()<<endl;
    cout<<"weak_ptr的引用数:"<<wPtr.use_count()<<endl;
    cout<<"ptr:"<<*ptr<<endl;
     shared_ptr<int> shared_ptr = wPtr.lock();
     ///ptr.reset(); 释放
     if (shared_ptr!= nullptr){
         cout<<"wPtr:"<<*shared_ptr<<endl;
     } else{

     }   cout<<"ptr 被释放"<<endl;
}


```

# 动态数组
- `int *p=new int[10]`
    - `P` 是一个数组元素类型的指针,而不是数组类型的对象
- `new int[10]`和`new int[10]()`
    - 后者会提供默认类型的元素初始化
- 释放 动态数组
    - `delete [] p`
    - 动态数组必须这样释放
> 指向的是数组的首元素

- 动态分配空数组

```c++
int *p =new  int [10];
int i=0;
cout<<"循环赋值"<<endl;
for (int * q = p;  q!=p+10 ; ++q) {
    *q=i;
    i++;
}
cout<<"输出结果"<<endl;
for (int * q = p;  q!=p+10 ; ++q) {
    cout<<*q<<endl;
}
```

- 智能指针和动态数组
    - `unique_ptr`支持下标操作
    - `shared_ptr` 不支持 直接管理动态数组,必须提供 删除器

- 例子

```C++

void arrayPtr(){
    int max=2;
    unique_ptr<int []> arrayUptr(new int[max]{1,2});
    for (int i = 0; i < max; ++i) {
        cout<<arrayUptr[i]<<endl;
    }
    shared_ptr<int []> arraySptr(new int[max]{3,4},
    [](int *p) {cout<<"删除器被调用"<<endl;delete [] p;});
    for (int j = 0; j <max ; ++j) {
        int i = *(arraySptr.get() + j);
        cout << "i=" << i << endl;
    }
}
```
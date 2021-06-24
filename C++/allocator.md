# allocator

- 定义在 `memory` 头文件中
- 释放内存时,必须要调用`destroy`

|                       |                                                            描述                                                            |
|:---------------------:|:--------------------------------------------------------------------------------------------------------------------------:|
|   `allocator<T> a`    |                                               可以为T分配内存的allocator对象                                               |
|   `a.allocator(n)`    |                                           分配一段可以保存n个T类型对象的原始内存                                           |
|  `a.deallocate(p,n)`  | 释放指针P中地址开始的内存,p必须是allocate返回的指针,n必须是p创建时要求的大小,调用此方法必须要为内存中创建的对象用`destroy` |
| `a.construct(p,args)` |                      p必须是一个T*类型的指针,指向一块原始内存,arg传递给T构造函数用来在p中构造一个对象                      |
|    `a.destroy(p)`     |                                                 对p指向的对象执行析构函数                                                  |

- 例子

```c++
void allocatorTest(){
    allocator<string> alloc;
    int max=10;
    string * v=alloc.allocate(max);
    int i=1;
    //赋值
    for (  auto v1 =v; v1!=v+max ;++v1) {
        alloc.construct(v1,i,'c');
        i++;
    }
    cout<<"输出结果"<<endl;
    for (  auto v1 =v; v1!=v+max ;++v1) {
        cout<<"v1="<<*v1<<endl;
    }
    //调用析构
    for (auto v2=v+10; v2!=v  ;) {
        alloc.destroy(v2--);
    }
    cout<<"调释放内存"<<endl;
    //释放内存
    alloc.deallocate(v,max);
}

```

- 相关的填充算法

|                                |                            描述                             |
|:------------------------------:|:---------------------------------------------------------:|
|  `uninitialized_copy(b,e,b2)`  | 拷贝迭代器b和e之间的元素到b2未指定的原始内存中,b2必须足够大 |
| `uninitialized_copy_n(b,n,b2)` |              拷贝从迭代器b开始的n个元素到b2中               |
|  `uninitialized_fill(b,e,t)`   |              在迭代器b和e之间的对象都为t的拷贝              |
| `uninitialized_fill_n(b,n,t)`  |               从迭代器b开始的内存创建n个对象                |

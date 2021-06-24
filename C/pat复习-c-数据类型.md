# 数据类型

- 基本数据类型

| 基本类型 | 字长 |
|:--------:|:----:|
|   char   |  1   |
|   int    |  2   |
|  float   |  4   |
|  double  |  8   |
|   void   |  0   |

- 数据类型修饰符号

|  修饰符  |          备注          |
|:--------:|:----------------------:|
| unsigned |         无符号         |
|  signed  | 与无修饰基本类型无区别 |
|  short   |   只能修饰int 字节/2   |
|   long   |   只能修饰int 字节*2   |

- 示例清单

``` c
#include <stdio.h>
int main() {
   printf("| char | %d|\n", sizeof(char));
   printf("| short | %d|\n", sizeof(short));
   printf("| short int | %d|\n", sizeof(short int));
   printf("| int | %d|\n", sizeof(int));
   printf("| long int | %d|\n", sizeof(long int));
   printf("| long long | %d|\n", sizeof(long long));
   printf("| long long int| %d|\n", sizeof(long long int));
   printf("| long | %d|\n", sizeof(long));
   printf("| float | %d|\n", sizeof(float));
   printf("| double | %d|\n", sizeof(double));
  return 0;
}

```

- 运行结果
  - 本机 64bit gcc
  
|     类型      | 长度 |
|:-------------:|:----:|
|     char      |  1   |
|     short     |  2   |
|   short int   |  2   |
|      int      |  4   |
|   long int    |  4   |
|   long long   |  8   |
| long long int |  8   |
|     long      |  4   |
|     float     |  4   |
|    double     |  8   |

- [某在线编译器](http://codepad.org)

|     类型      | 长度 |
|:-------------:|:----:|
|     char      |  1   |
|     short     |  2   |
|   short int   |  2   |
|      int      |  4   |
|   long int    |  4   |
|   long long   |  8   |
| long long int |  8   |
|     long      |  4   |
|     float     |  4   |
|    double     |  8   |

- [某在线编译器](https://c.runoob.com/compile/11)

|     类型      | 长度 |
|:-------------:|:----:|
|     char      |  1   |
|     short     |  2   |
|   short int   |  2   |
|      int      |  4   |
|   long int    |  8   |
|   long long   |  8   |
| long long int |  8   |
|     long      |  8   |
|     float     |  4   |
|    double     |  8   |

## 变量相关

- 内存模型
  - bss段
    - 存放未初始化的全局变量的内存区域(静态内存分配)
  - 数据段
    - 以经初始化的全局变量的内存区域(静态内存分配)
  - 代码段
    - 存放执行代码的内存区域
  - 堆
    - 存放运行中被动态分配的内存段
  - 栈
    - 存放程序的局部变量 不包含static修饰的变量

- 相关关键字
  - `#define`
    - 预编译 宏指令
      - 对指定的内容做替换 没有类型 更没有类型检查
        - 一般用作定义某型常量
  - `typedef`
    - 定义一个别名
  - `extern`
    - 作用为连接变量为全局变量
  - `const`
    - 定义一个常量
  - `static`
    - 修饰全局变量的，这个全局变量只能在本文件中访问，不能在其它文件中访问
    - 不会被释放
    - 拥有默认的初始值
  - `auto`
    - 局部变量,智能修饰局部变量,编译器一般会自动添加
  - `register`
    - 将变量定义在存储在寄存器中(尽可能),不能进行&运算

  - `static`的例子

```c
#include <stdio.h>
 void  addCount(){
  int count=0;
  printf("%d\n",++count );
}
void staticAddCount(){
  static int count=0;
  printf("%d\n",++count );
}
int main() {
  staticAddCount();
  staticAddCount();
  staticAddCount();
  addCount();
  addCount();
  addCount();
  return 0;
}
```

> static 修饰方法时 只能在本文件调用

## 结构体

- 类的雏形
  - 用于表示一组数据

- 例子

```c
#include <stdio.h>
struct User{
  char * name;
  int age;
  double money;
};
void  printfUser(struct User user2){
  printf("name:%s\n",user2.name );
  printf("age:%d\n",user2.age );
  printf("money:%f\n",user2.money );

}
int main() {
  struct User s;
  s.name="ccxh";
  s.age=17;
  s.money=10.1;

  /*使用指针取成员变量*/
  struct User * p_user=&s;
  printf("%d\n",p_user->age);
  printfUser(s);
  printf("%d\n",sizeof(s));
  return 0;
}
```

- 例子2

```c
#include <stdio.h>
 typedef struct{
  unsigned char i:1;
} FLAG;
struct{
  int x;
}xx;
int main() {
  /*通过typedif 简写 别名 变量名 (struct 结构体名 变量名)*/
  FLAG f={1};
  printf("%d\n",f.i );
  /*溢出了1位*/
  f.i=2;
  printf("%d\n",f.i );
    printf("长度:%d\n",sizeof(f));
  return 0;
}

```

## 公用体

- 字节长度决定是最长的成员变量

- 例子

```c
#include <stdio.h>
 typedef union {
  unsigned int x :1;
  unsigned int x1 :2;
} X;
int main() {
  X s;
  printf("%d\n",sizeof(s) );
  s.x1=3;
  s.x=0;
  printf("%d\n",s.x );
  printf("%d\n",s.x1 );
  return 0;
}
```

> 公用同一块内存

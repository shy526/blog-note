# 指针

- 简单理解就是一个指向内存地址的变量
  - 既java中的引用类型
    - 当然这是java包装好的指针

## 指针声明

- type * var_name
  - `int * p_a;`
    - 指向int类型的指针

## 指针例子

- 指针基本了解

```c
#include <stdio.h>
int main() {
  int v=100;
  //&取地址符号
  int *p=&v;
  printf("指针p指向的地址为:%p\n",p );
  //* 为解引用符
  printf("指针p指向的地址的值为:%d\n",*p);
  v=1;
  //修改后 v的值之后
  printf("修改v的值\n" );
  printf("指针p指向的地址为:%p\n",p );
  // *p的值也发生变化
  printf("指针p指向的地址的值为:%d\n",*p);

  //修改*p的值
  *p=10;
  printf("v的值:%d\n",v );
  printf("指针p指向的地址为:%p\n",p );
  printf("指针p指向的地址的值为:%d\n",*p);
  printf("-----------------------------------------------指针的默认值-------------------------------------\n" );

  int * j;
  //没有默认值时 地址是随机的,一般将指针的初始值设置为NULL
  printf("指针j指向的地址为:%p\n",j );
 //printf("指针j指向的地址的值为:%d\n",*j);
j=NULL;
//一般将指针的初始值设置为NULL
printf("指针j指向NULL的地址为:%p\n",j );
//  printf("指针j指向的地址的值为:%d\n",*j);  导致程序中断
  //指针的运算
  printf("-----------------------------------------------指针的运算-------------------------------------\n" );
  int vs[]={1,3,5,111,7,9,10,4};
  //定一个指向数组的指针,数组名表示首地址
  int * p_vs=vs;
  printf("指针j指向的地址为:%p\n",p_vs);
  printf("指针j指向的地址的值为:%d\n",*p_vs);
  //指针累加
  p_vs++;
  //可见数组的内存是连续的
  printf("指针j指向的地址为:%p\n",p_vs);
  printf("指针j指向的地址的值为:%d\n",*p_vs);
  p_vs--;
  //输出又为1
  printf("指针j指向的地址为:%p\n",p_vs);
  printf("指针j指向的地址的值为:%d\n",*p_vs);
  //输出大于5的数
  printf("---------------------------------------输出大于5的数-------------------------------------\n" );
  //int standard=vs[];
  int *p_standard=&vs[2];
  printf("*p_standard为:%d\n",*p_standard);
  int len=sizeof(vs)/sizeof(*p_vs);
   for(int i=0;i<8;i++){
       if (&vs[i]>p_standard) {
         //实际还是比的是地址,只输出了高地址位的数
           printf("%d\n",vs[i]);
       }
   }
  return 0;
}

```

- 指针数组
  - 多个指针的集合

```c
#include <stdio.h>
int main() {
  char* p_v[]={"tome","joke","kater","sarba","show"};
  for(int i=0;i<5;i++){
    printf("p_v[%d]=%p\n",i,p_v[i]);
    printf("*p_v[%d]=%s\n",i,p_v[i]);
      printf("---------------\n");
  }
  printf("%c\n",*p_v[4]);
  //一般可以把这种指针指向char数组

  return 0;
}

```

- 指向指针的指针

```c
#include <stdio.h>
int main() {
  int v=100;
  int* p_v=&v;
  int** pp_v=&p_v;
  int *** ppp_v=&pp_v;
  printf("%p\n",p_v);
  printf("%p\n",pp_v );
  printf("%d\n",**pp_v );
  printf("%d\n",***ppp_v );

  //单层解引用 相当于输出p_v地址的十进制数
  printf("%d\n",*pp_v );
  return 0;
}
```

- 函数指针和回调
  - 让我想起了回调的巨坑

```c
#include <stdio.h>
int sum(int x,int y){
   return x+y;
}
int diffe(int x,int y){
   return x-y;
}
//一个回调
int call(int (*callback)(int,int)){
  callback(1,2);
}
int main() {
  //函数指针
  int (*p)(int,int)=&sum;
  int sum=p(1,2);
  printf("%d\n",sum );
  int callVar=call(p);
  printf("%d\n",callVar );
  p=&diffe;
  int callVar1=call(p);
  printf("%d\n",callVar1 );
}

```

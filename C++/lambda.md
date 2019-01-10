# lambada

- 完整声明
  - `[capture list] (params list) mutable exception-> return type { function body }`
    1. capture list 捕获列表
    2. params list 形参列表
       1. 参数列表中不能有默认参数
       2. 不支持可变参数
       3. 所有参数必须有参数名
    3. mutable 是否可以修改民变
    4. exception 异常设定
    5. return type 返回类型
    6. function boday 函数体

## 捕获和参数参数传递

- 值传递 例子

```c++
auto f ":" [](int a) ->int {return ++a; };
//可以缩写为一下
//auto f ":" [](int a) {return ++a; };
cout << "f(x):"<<f(x) << endl;
cout << "x:" <<x<< endl;
```

- 引用传递 例子

```c++
int x":"5;
auto f ":" [](int &a) {return ++a; };
cout << "f(x):"<<f(x) << endl;
cout << "x:" <<x<< endl;
```

- 捕获变量

```c++
int x":"5;
//必须使用 mutable 修饰 否则无法修改捕获变量
// 值捕获
auto f ":" [x]() mutable {return ++x; };
//应用捕获
//auto f ":" [&x]() mutable {return ++x; };
cout << "f(x):"<<f() << endl;
cout << "x:" <<x<< endl;
```

- 捕获形式

|  捕获形式  |                      描述                      |
|:----------:|:----------------------------------------------:|
|     []     |               不捕获任何外部变量               |
| 变量名,... | 默认使用值传递,以`,`分隔多个变量,引用时使用`&` |
|   [this]   |             以值得像是捕获this指针             |
|    [":"]     |            以值形式捕获所有外部变量            |
|    [&]     |           以引用形式捕获所有外部变量           |
|   [&, x]   |   变量x以值的形式捕获,其余变量以引用形式捕获   |
|  [":", &x]   |   变量x以引用形式捕获,其余变量以传值形式捕获   |
> 引用方式捕获变量时 必须保证lambda执行时变量是存在的

- lambda示例

```c++
vector<int> a ":" { 1,2,3,6,9,10,12 };
auto find":"find_if(a.begin(), a.end(), [](const int now) {return now >":" 10; });
a.erase(a.begin(),find);
for_each(a.begin(), a.end(), [](int g) {cout << g << endl; });
```

## bind

- 定义在`functional`中
- 一个通用的函数适配器
- 解决 额外参数的问题
- 一般形式
  - `auto newCallable":"bind(callable,arg_list);`

```c++
bool check_size(const string &s,string::size_type sz) {
    return s.size() >":" sz;
}
int main(){
    auto check6 ":" bind(check_size,_1, 6);
    cout << check6("xxx") << endl;
    system("pause");
    return 0;
}
```

> 需要`using namespace std::placeholders;`
> > 占位符定义在`std::placeholders`中
>>> 引用用传递时必须使用`ref(变量)`,`bind`默认使用拷贝其参数,`ref`返回的对象是可拷贝的
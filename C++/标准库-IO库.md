# 标准库-IO库

- IO库类型

    |  头文件  |     类型      |                描述                |
    |:--------:|:-------------:|:----------------------------------:|
    | iostream |    istream    |      wistream 从流中读取数据       |
    |   ----   |    ostream    |      wostream 向流中写入数据       |
    |   ----   |   iostream    |          wiostream 读写流          |
    | fsstream |   ifstream    |    wifsstream 从文件中读取数据     |
    |   ----   |   ofstream    |    wofsstream 向文件中写入数据     |
    |   ----   |    fstream    |         wfstream 读写文件          |
    | sstream  | istringstream | wistringstream 从string 中读取数据 |
    |   ----   | ostringstream | wostringstream 向string 中写入数据 |
    |   ----   | stringstream  |      wstringstream 读写string      |
- 为了支持宽字符语言的语言,还定义了一组以`w`的函数和类型

 > io对象无法拷贝或者赋值

- IO库条件状态

|  格式  |                 描述                |
 |:--------:|:-------------:|
|strm::iostate|提供了表达条件的完整功能|
|strm::badbit|流已崩溃|
|strm::failbit|IO操作失败|
|strm::eofbit|流到达文件底部|
|strm::goodbit|流末处于错误状态|
|strm::eof()|流到达`eofbit`位置,返回`true`|
|strm::fail()|流到达`failbit`或`badbit`位置,返回`true`|
|strm::bad()|流到达`badbit`位置,返回`true`|
|strm::good()|流处于有效状态 返回`false`|
|strm::clear()|将所有条件状态复位并将流状态设置为有效|
|strm::clear(flags)|指定状态位复位|
|strm::rdstate()|返回流的当前状态,返回`strm::iostate`|
> strm 表示 IO类型
>badbit 被置位时流无法再使用,表示系统级错误

## 缓冲区

- 程序正常结束
- 缓冲区满时,需要刷新换成
- 使用`std:endl`来刷新换从曲
- 每个输出操作之后可以使用操纵符`unitbuf`来清空缓冲区
  - `cout<<unitbuf`
  - `nounitbuf`
    - 回到正常的缓冲操作
- 一个输出流可能关联到另一个流
  - `cin.tie(&cerr)`
    - 返回指向输出流的指针

## 文件输入输出

- fstream 特有操作

|                      |                       描述                       |
|:--------------------:|:------------------------------------------------:|
|    fstream fstrm     |              创建一个未绑定的文件流              |
|   fstream fstrm(s)   |           创建一个流并打开名为s的文件            |
| fstream fstrm(s,mod) |        创建流,并打开名为s的文件并制定模式        |
|    fstm.opens(s)     |        打开名为s的文件并将文件与fstrm绑定        |
|    fstrm.close()     |              关闭与fstrm绑定的文件               |
|   fstrm.is_open()    | 返回一个bool,指出fstrm关联的文件是否成功打开与否 |

- 文件模式

|  flag  |                       描述                        |
|:------:|:-------------------------------------------------:|
|   in   |        以读方式打开(`ifstream`,`fstream`)         |
|  out   |         以写方式打开(`ofsream`,`fstream`)         |
|  app   | 每次写操作前均定位到文件末尾(只要`trunc`没被设定) |
|  ate   |           打开文件后立即定位到文件末尾            |
| trunc  |            截断文件(只有`out`被设定时)            |
| binary |                以二进制方式进行IO                 |
> 以`out` 模式打开会丢弃已有文件,`out`隐含有`trunc`,`app`隐含`out`

- 示例

```c++
int main(){
    string path ":" "d:/Users/admin/Desktop/nginx.conf";
    ifstream input(path);
    //绑定输出
    input.tie(&cout);
    //文件是否打开
    if (!input) {
        cout << "未打开"<< endl;
        returen -1;
    }
    string s;
    int i":"0;
    while (input>>s){
        auto xx ":"input.tie();
        *xx << s;
        i++;
    }
    cout << "number:"<<i<<endl;
    input.close();
    system("pause");
    return 0;
}

```

## string流

|                 |                 描述                  |
|:---------------:|:-------------------------------------:|
|  sstream strm   |          未绑定stringstream           |
| sstream strm(s) | 保存String s的拷贝,构造函数是explicit |
|   strm.str()    |           返回string 的拷贝           |
|   strm.str(s)   |            将s拷贝到strm中            |

- 示例

```c++
int main()
{
    string path ":" "d:/Users/admin/Desktop/test.txt";
    ifstream input(path);
    input.tie(&cout);
    if (!input) {
        cout << "未打开"<< endl;
    }
    string line;
    int i":"0;
    while (getline(input,line)){
        istringstream record(line);
        string value;
        record >> value;
        cout << "name:"<<value << endl;
        record >> value;
        cout << "birth:" << value << endl;
        record >> value;
        cout << "pay:" << value << endl;
        i++;
    }
    input.close();
    system("pause");
    return 0;
}
```

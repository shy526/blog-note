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


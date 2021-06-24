# restful请求中文乱码问题

## 排错

- 含有中文参数时导致400错误
  - 请求错误
- 错误原因
  - `url : "http://sso.tb.com/user/check/"+escape(pin)`
  - 由于参数中含有%导致
- 修改为
  - `escape(pin)`-->`encodeURI(pin)`
    - 中文乱码

- 又修改为
  - `encodeURI(pin)`->`encodeURI(encodeURI(pin))`
  - Controller层添加代码
        - `param=URLDecoder.decode(param,"utf-8");`
  - 解决乱码问题

## 错误相关环境及代码

- jdk 1.7
- nginx
  - 负载均衡
- redis
  - 集群

- web.xml
  - 拦截器 配置

```XML
<filter>
    <filter-name>encodingFilter</filter-name>
    <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
    <init-param>
        <param-name>encoding</param-name>
        <param-value>UTF-8</param-value>
    </init-param>
</filter>
<filter-mapping>
    <filter-name>encodingFilter</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>
```

- 相关js发送代码

```javascript
  $.ajax({
    /*url : "http://sso.yiqihuo.com/user/check/"+escape(pin)+"/1?r=" + Math.random(),
         //错误代码
    */
       url : "http://sso.yiqihuo.com/user/check/"+encodeURI(encodeURI(pin))+"/1?r=" + Math.random(),
        dataType : "jsonp",
        success : function(data) {
            checkpin = data.data?"1":"0";
            if (!data.data) {
                validateSettings.succeed.run(option);
                namestate = true;
            }else {
                validateSettings.error.run(option, "该用户名已占用！");
                namestate = false;
            }
        }
    });

```

```java
@RequestMapping("/user/check/{param}/{type}")
@ResponseBody
public Object check(String callback,
                @PathVariable String param,
                @PathVariable Integer type) {
    try {
        param=URLDecoder.decode(param,"utf-8");
        //二次
        Boolean b=userService.check(type,param);
        MappingJacksonValue mjv=new MappingJacksonValue(SysResult.oK(b));
        mjv.setJsonpFunction(callback);
        return mjv;
    } catch (Exception e) {
        e.printStackTrace();
        return SysResult.build(201,"校验信息时出错");
    }


}
```

## 错误原因及一些分析

- `escape(pin)`
  - springmvc配置了转码,导致无法转码抛出异常
    - `URLDecoder`
  - 示例
    - 400错误

- `encodeURI(pin)`
  - 导致乱码
  - 原因未知
    - 可能%号未被真确理解
  - 示例
    - sundada孙->sundadaå­

- `encodeURI(encodeURI(pin))`
  - 没有问题
  - 需要额外解一次码
  - 示例
    - sundada孙->sundada%25E5%25AD%2599->sundada%E5%AD%99->sundada孙(额外解码)

- `escape(escape(pin))`
  - 尝试了一次,二次解码报错
    - 没有使用对应的解码方式导致 `URLDecoder.decode`抛出异常
    - 示例
    - sundada孙->sundada%25u5B59->sundada%u5B59->抛出异常

## js中三种编码方式

### escape()

- 函数可对字符串进行编码，这样就可以在所有的计算机上读取该字符串
- 其中某些字符被替换成了十六进制的转义序列
      - %uxxx

- 该方法不会对 ASCII 字母和数字进行编码，也不会对下面这些 ASCII 标点符号进行编码： - _ . ! ~ * ' ( )

### encodeURI()

- 把字符串作为 URI 进行编码 utf-8
      - %x
- 该方法不会对 ASCII 字母和数字进行编码，也不会对这些 ASCII 标点符号进行编码： - _ . ! ~ * ' ( )
- 不会进行转义的：;/?:@&=+$,#

### encodeURIComponent()

- 字符串作为 URI 组件进行编码
- 方法不会对 ASCII 字母和数字进行编码，也不会对这些 ASCII 标点符号进行编码： - _ . ! ~ * ' ( )

#### 对比

- 三种方法都不会对 ASCII 字母、数字和规定的特殊 ASCII 标点符号进行编码
  - 其余都替换为十六进制转义序列

#### 引入工具escape

- 使用工具类来进行解码escape编码的文件

```JAVA
   private final static String[] hex = {
        "00","01","02","03","04","05","06","07","08","09","0A","0B","0C","0D","0E","0F",
        "10","11","12","13","14","15","16","17","18","19","1A","1B","1C","1D","1E","1F",
        "20","21","22","23","24","25","26","27","28","29","2A","2B","2C","2D","2E","2F",
        "30","31","32","33","34","35","36","37","38","39","3A","3B","3C","3D","3E","3F",
        "40","41","42","43","44","45","46","47","48","49","4A","4B","4C","4D","4E","4F",
        "50","51","52","53","54","55","56","57","58","59","5A","5B","5C","5D","5E","5F",
        "60","61","62","63","64","65","66","67","68","69","6A","6B","6C","6D","6E","6F",
        "70","71","72","73","74","75","76","77","78","79","7A","7B","7C","7D","7E","7F",
        "80","81","82","83","84","85","86","87","88","89","8A","8B","8C","8D","8E","8F",
        "90","91","92","93","94","95","96","97","98","99","9A","9B","9C","9D","9E","9F",
        "A0","A1","A2","A3","A4","A5","A6","A7","A8","A9","AA","AB","AC","AD","AE","AF",
        "B0","B1","B2","B3","B4","B5","B6","B7","B8","B9","BA","BB","BC","BD","BE","BF",
        "C0","C1","C2","C3","C4","C5","C6","C7","C8","C9","CA","CB","CC","CD","CE","CF",
        "D0","D1","D2","D3","D4","D5","D6","D7","D8","D9","DA","DB","DC","DD","DE","DF",
        "E0","E1","E2","E3","E4","E5","E6","E7","E8","E9","EA","EB","EC","ED","EE","EF",
        "F0","F1","F2","F3","F4","F5","F6","F7","F8","F9","FA","FB","FC","FD","FE","FF"
    };
    private final static byte[] val = {
        0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,
        0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,
        0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,
        0x00,0x01,0x02,0x03,0x04,0x05,0x06,0x07,0x08,0x09,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,
        0x3F,0x0A,0x0B,0x0C,0x0D,0x0E,0x0F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,
        0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,
        0x3F,0x0A,0x0B,0x0C,0x0D,0x0E,0x0F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,
        0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,
        0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,
        0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,
        0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,
        0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,
        0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,
        0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,
        0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,
        0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F,0x3F
    };
    public static String escape(String s) {
        StringBuffer sbuf = new StringBuffer();
        int len = s.length();
        for (int i = 0; i < len; i++) {
            int ch = s.charAt(i);
            if (ch == ' ') {                        // space : map to '+'
                sbuf.append('+');
            } else if ('A' <= ch && ch <= 'Z') {    // 'A'..'Z' : as it was
                sbuf.append((char)ch);
            } else if ('a' <= ch && ch <= 'z') {    // 'a'..'z' : as it was
                sbuf.append((char)ch);
            } else if ('0' <= ch && ch <= '9') {    // '0'..'9' : as it was
                sbuf.append((char)ch);
            } else if (ch == '-' || ch == '_'       // unreserved : as it was
                || ch == '.' || ch == '!'
                || ch == '~' || ch == '*'
                || ch == '/'' || ch == '('
                || ch == ')') {
                sbuf.append((char)ch);
            } else if (ch <= 0x007F) {              // other ASCII : map to %XX
                sbuf.append('%');
                sbuf.append(hex[ch]);
            } else {                                // unicode : map to %uXXXX
                sbuf.append('%');
                sbuf.append('u');
                sbuf.append(hex[(ch >>> 8)]);
                sbuf.append(hex[(0x00FF & ch)]);
            }
        }
        return sbuf.toString();
    }
    public static String unescape(String s) {
        StringBuffer sbuf = new StringBuffer();
        int i = 0;
        int len = s.length();
        while (i < len) {
            int ch = s.charAt(i);
            if (ch == '+') {                        // + : map to ' '
                sbuf.append(' ');
            } else if ('A' <= ch && ch <= 'Z') {    // 'A'..'Z' : as it was
                sbuf.append((char)ch);
            } else if ('a' <= ch && ch <= 'z') {    // 'a'..'z' : as it was
                sbuf.append((char)ch);
            } else if ('0' <= ch && ch <= '9') {    // '0'..'9' : as it was
                sbuf.append((char)ch);
            } else if (ch == '-' || ch == '_'       // unreserved : as it was
                || ch == '.' || ch == '!'
                || ch == '~' || ch == '*'
                || ch == '/'' || ch == '('
                || ch == ')') {
                sbuf.append((char)ch);
            } else if (ch == '%') {
                int cint = 0;
                if ('u' != s.charAt(i+1)) {         // %XX : map to ascii(XX)
                    cint = (cint << 4) | val[s.charAt(i+1)];
                    cint = (cint << 4) | val[s.charAt(i+2)];
                    i+=2;
                } else {                            // %uXXXX : map to unicode(XXXX)
                    cint = (cint << 4) | val[s.charAt(i+2)];
                    cint = (cint << 4) | val[s.charAt(i+3)];
                    cint = (cint << 4) | val[s.charAt(i+4)];
                    cint = (cint << 4) | val[s.charAt(i+5)];
                    i+=5;
                }
                sbuf.append((char)cint);
            }
            i++;
        }
        return sbuf.toString();
    }
```

## 参考

- `http://blog.csdn.net/hbzyaxiu520/article/details/5607873`

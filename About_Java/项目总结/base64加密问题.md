# base64 加密问题

> 本篇笔记参考自博客：[为什么Base64编码有回车换行符](https://blog.csdn.net/jifengwan/article/details/45460695)

![1566284183174](D:\GitBook\About_Java\项目总结\assets\1566284183174.png)

### 一、问题概述

使用 Base64Utils 加密后，出现了`\r\n`换行，导致数据对接出现异常。

````java
import sun.misc.BASE64Decoder;
import sun.misc.BASE64Encoder;

import java.io.UnsupportedEncodingException;

public class Base64Utils {
    /**
     * 加密
     * @param str
     * @param code
     * @return
     */
    public static String getBase64(String str,String code) {
        byte[] b = null;
        String s = null;
        try {
            b = str.getBytes(code);
        } catch (UnsupportedEncodingException e) {
            e.printStackTrace();
        }
        if (b != null) {
            s = new BASE64Encoder().encode(b);
        }
        return s;
    }

    /**
     * 解密
     * @param s
     * @param code
     * @return
     */
    public static String getFromBase64(String s,String code) {
        byte[] b = null;
        String result = null;
        if (s != null) {
            BASE64Decoder decoder = new BASE64Decoder();
            try {
                b = decoder.decodeBuffer(s);
                result = new String(b, code);
            } catch (Exception e) {
                e.printStackTrace();
            }
        }
        return result;
    }
}
````

据博客所介绍，在 RFC2045 中有规定：

The encoded output stream must be represented in lines of no more than 76 characters each.
*（Base64 一行不能超过 76 字符，超过则添加回车换行符。）*

这里需要注意的是：

**回车换行符（\r\n）是在 Windows 才有，而 Linux 只有换行（\n），Mac 只有回车（\r）。**

因此在代码中对加密后的字符串进行处理，调用字符串对象的`replaceAll`方法替换掉`\r`和`\n`

````java
// 替换回车和换行符，兼容不同平台
字符串对象.replaceAll("\r|\n", "");
````
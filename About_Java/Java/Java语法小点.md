# Java 语法小点

## 一、Java 中斜杠的使用

Window 系统默认的路径分隔符是`\`
Linux 系统默认的路径分隔符是`/`
Java 跨平台，而且`\`为转译符号，因此在处理 URL 和文件路径等情况，需要`\\` = `/`，或者用`File.separator`进行组合

## 二、Java 二维数组的声明

* 规则的：

  ````java
  int a[][] = new int[2][3];
  int[][] b = {{1,2,3},{4,5,6}};
  ````

* 不规则的：

  ````java
  int c[][] = new int[3][];
  c[0] = new int[3];
  c[1] = new int[2];
  ````

## 三、Java 字符串转数组

````java
private String[] string2Array(String str) {
    String[] strs;
    if (str != null && str.length() > 2 && str.startsWith("[")) {
        str = str.replace(" ", "").replaceAll("\"", "");
        str = str.substring(1, str.length() - 1);
        strs = str.split(",");
    } else {
        strs = new String[]{str};
    }
    return strs;
}

// 这里注意，如果是.replace()或.startsWith()方法，直接使用"["或"]"即可；但是因为.replaceAll()方法使用的是正则匹配，所以需要对中括号进行转译
String image = images.replaceAll("\\[", "").replaceAll("\\]", "").replaceAll("\"", "");
````


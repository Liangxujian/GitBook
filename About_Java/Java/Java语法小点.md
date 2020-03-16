#### Java 语法小点

##### 一、Java 中斜杠的使用

window 系统默认的路径分隔符是`\`
linux 系统默认的路径分隔符是`/`
Java 跨平台，而且`\`为转译符号，
所以`\\` = `/`，或者用`File.separator`进行组合

##### 二、Java 二维数组的声明

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

  

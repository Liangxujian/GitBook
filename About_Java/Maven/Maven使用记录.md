# Maven 使用记录

## 一、使用 maven 导入本地jar包

````bash
mvn install:install-file -Dfile=jar包路径 -DgroupId=pom.xml对应groupId -DartifactId=pom.xml对应artifactId -Dversion=pom.xml对应version -Dpackaging=jar
# 示例
mvn install:install-file -Dfile=D://ueditor-1.1.12.jar -DgroupId=org.chengxi -DartifactId=ueditor -Dversion=1.1.12 -Dpackaging=jar
````


# Introduction

##### 3.5.2 Map 集合存储 null 情况

开始

| 集合类            | Key           | Value         | Super       | 说明                   |
| ----------------- | ------------- | ------------- | ----------- | ---------------------- |
| HashTable         | 不允许为 null | 不允许为 null | Dictionary  | 线程安全               |
| ConcurrentHashMap | 不允许为 null | 不允许为 null | AbstractMap | 锁分段技术（JDK8:CAS） |
| TreeMap           | 不允许为 null | 允许为 null   | AbstractMap | 线程不安全             |
| HashMap           | 允许为 null   | 允许为 null   | AbstractMap | 线程不安全             |
|                   |               |               |             |                        |

结束
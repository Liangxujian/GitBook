# MySQL 语句性能 explain

```sql
explain
SELECT a.nno_id, a.shop_name, a.shop_url, a.company_name
FROM tb_ns_network_object AS a RIGHT JOIN
    (SELECT tnno.nno_id
    FROM tb_ns_network_object tnno
    WHERE 1 = 1
    ORDER BY tnno.create_time desc,tnno.nno_id DESC LIMIT 0,10 ) AS b
ON a.nno_id=b.nno_id
```

![image-20201102204214001](assets\image-20201102204214001.png)

**字段说明**

| 字段名称      | 注释                                                         |
| ------------- | ------------------------------------------------------------ |
| id            | SELECT识别符，是SELECT的查询序列号                           |
| select_type   | SELECT类型<br/>1.SIMPLE： 简单SELECT(不使用UNION或子查询)<br/>2.PRIMARY： 最外面的SELECT<br/>3.UNION：UNION中的第二个或后面的SELECT语句<br/>4.DEPENDENT UNION：UNION中的第二个或后面的SELECT语句，取决于外面的查询<br/>5.UNION RESULT：UNION的结果<br/>6.SUBQUERY：子查询中的第一个SELECT<br/>7.DEPENDENT SUBQUERY：子查询中的第一个SELECT，取决于外面的查询<br/>8.DERIVED：导出表的SELECT(FROM子句的子查询) |
| table         | 表名                                                         |
| type          | 联接类型（这个最能反映sql语句的性能，从上到下为性能递减）<br/>1.system：表仅有一行(=系统表)。这是const联接类型的一个特例。<br/>2.const：表最多有一个匹配行，它将在查询开始时被读取。因为仅有一行，在这行的列值可被优化器剩余部分认为是常数。const用于用常数值比较PRIMARY KEY或UNIQUE索引的所有部分时。<br/>3.eq_ref：对于每个来自于前面的表的行组合，从该表中读取一行。这可能是最好的联接类型，除了const类型。它用在一个索引的所有部分被联接使用并且索引是UNIQUE或PRIMARY KEY。eq_ref可以用于使用= 操作符比较的带索引的列。比较值可以为常量或一个使用在该表前面所读取的表的列的表达式。<br/>4.ref：对于每个来自于前面的表的行组合，所有有匹配索引值的行将从这张表中读取。如果联接只使用键的最左边的前缀，或如果键不是UNIQUE或PRIMARY KEY(换句话说，如果联接不能基于关键字选择单个行的话)，则使用ref。如果使用的键仅仅匹配少量行，该联接类型是不错的。ref可以用于使用=或<=>操作符的带索引的列。<br/>5.ref_or_null：该联接类型如同ref，但是添加了MySQL可以专门搜索包含NULL值的行。在解决子查询中经常使用该联接类型的优化。<br/>6.index_merge：该联接类型表示使用了索引合并优化方法。在这种情况下，key列包含了使用的索引的清单，key_len包含了使用的索引的最长的关键元素。<br/>7.unique_subquery：该类型替换了下面形式的IN子查询的ref：value IN (SELECT primary_key FROMsingle_table WHERE some_expr);unique_subquery是一个索引查找函数，可以完全替换子查询，效率更高。<br/>8.index_subquery：该联接类型类似于unique_subquery。可以替换IN子查询，但只适合下列形式的子查询中的非唯一索引：`value IN (SELECT key_column FROM single_table WHERE some_expr)`<br/>9.range：只检索给定范围的行，使用一个索引来选择行。key列显示使用了哪个索引。key_len包含所使用索引的最长关键元素。在该类型中ref列为NULL。当使用=、<>、>、>=、<、<=、IS NULL、<=>、BETWEEN或者IN操作符，用常量比较关键字列时，可以使用range。<br/>10.index：该联接类型与ALL相同，除了只有索引树被扫描。这通常比ALL快，因为索引文件通常比数据文件小。<br/>11.all：对于每个来自于先前的表的行组合，进行完整的表扫描。如果表是第一个没标记const的表，这通常不好，并且通常在它情况下很差。通常可以增加更多的索引而不要使用ALL，使得行能基于前面的表中的常数值或列值被检索出。 |
| possible_keys | possible_keys列指出MySQL能使用哪个索引在该表中找到行。注意，该列完全独立于EXPLAIN输出所示的表的次序。这意味着在possible_keys中的某些键实际上不能按生成的表次序使用 |
| key           | key列显示MySQL实际决定使用的键(索引)。如果没有选择索引，键是NULL。要想强制MySQL使用或忽视possible_keys列中的索引，在查询中使用FORCE INDEX、USE INDEX或者IGNORE INDEX |
| key_len       | key_len列显示MySQL决定使用的键长度。如果键是NULL，则长度为NULL。注意通过key_len值我们可以确定MySQL将实际使用一个多部关键字的几个部分 |
| ref           | ref列显示使用哪个列或常数与key一起从表中选择行               |
| rows          | rows列显示MySQL认为它执行查询时必须检查的行数                |
| Extra         | 该列包含MySQL解决查询的详细信息<br/>1.Distinct：MySQL发现第1个匹配行后，停止为当前的行组合搜索更多的行。<br/>2.Not exists：MySQL能够对查询进行LEFT JOIN优化，发现1个匹配LEFT JOIN标准的行后，不再为前面的的行组合在该表内检查更多的行。<br/>3.range checked for each record (index map: #)：MySQL没有发现好的可以使用的索引，但发现如果来自前面的表的列值已知，可能部分索引可以使用。对前面的表的每个行组合，MySQL检查是否可以使用range或index_merge访问方法来索取行。<br/>4.Using filesort：MySQL需要额外的一次传递，以找出如何按排序顺序检索行。通过根据联接类型浏览所有行并为所有匹配WHERE子句的行保存排序关键字和行的指针来完成排序。然后关键字被排序，并按排序顺序检索行。<br/>5.Using index：从只使用索引树中的信息而不需要进一步搜索读取实际的行来检索表中的列信息。当查询只使用作为单一索引一部分的列时，可以使用该策略。<br/>6.Using temporary：为了解决查询，MySQL需要创建一个临时表来容纳结果。典型情况如查询包含可以按不同情况列出列的GROUP BY和ORDER BY子句时。<br/>7.Using where：WHERE子句用于限制哪一个行匹配下一个表或发送到客户。除非你专门从表中索取或检查所有行，如果Extra值不为Using where并且表联接类型为ALL或index，查询可能会有一些错误。<br/>8.Using sort_union(...), Using union(...), Using intersect(...)：这些函数说明如何为index_merge联接类型合并索引扫描。<br/>9.Using index for group-by：类似于访问表的Using index方式，Using index for group-by表示MySQL发现了一个索引，可以用来查询GROUP BY或DISTINCT查询的所有列，而不要额外搜索硬盘访问实际的表。并且，按最有效的方式使用索引，以便对于每个组，只读取少量索引条目。 |


### Join应用

#### Reduce Join

```
Map端工作
Reduce端工作
```

#### Map Join

```
引出Map Join：
```

### 计数器应用

### 数据清洗（ETL）

在运行核心业务MapReduce程序之前，往往要先对数据进行清洗，清理掉不符合用户要求的数据。清理的过程往往只需要运行Mapper程序，不需要运行Reduce程序。

### MapReduce开发总结

```
1. 输入数据接口：InputFormat
	（1）默认使用的实现类是：TextInputFormat 
	（2）TextInputFormat的功能逻辑是：一次读一行文本，然后将该行的起始偏移量作为key，行内容作为value返回
	（3）KeyValueTextInputFormat每一行均为一条记录，被分隔符分割为key，value。默认分隔符是tab（\t）。
	（4）NlineInputFormat按照指定的行数N来划分切片
	（5）CombineTextInputFormat可以把多个小文件合并成一个切片处理，提高处理效率。
	（6）用户还可以自定义InputFormat
```

```
2. 逻辑处理接口：Mapper 
	用户根据业务需求实现其中三个方法：map()   setup()   cleanup () 
```

```
3．Partitioner分区
	（1）有默认实现 HashPartitioner，逻辑是根据key的哈希值和numReduces来返回一个分区号；key.hashCode()&Integer.MAXVALUE % numReduces
	（2）如果业务上有特别的需求，可以自定义分区。
```

```
4．Comparable排序
	（1）当我们用自定义的对象作为key来输出时，就必须要实现WritableComparable接口，重写其中的compareTo()方法。
	（2）部分排序：对最终输出的每一个文件进行内部排序。
	（3）全排序：对所有数据进行排序，通常只有一个Reduce。
	（4）二次排序：排序的条件有两个。
```

```
5．Combiner合并
	Combiner合并可以提高程序执行效率，减少IO传输。但是使用时必须不能影响原有的业务处理结果。
```

```
6．Reduce端分组：GroupingComparator
    在Reduce端对key进行分组。应用于：在接收的key为bean对象时，想让一个或几个字段相同（全部字段比较不相同）的key进入到同一个reduce方法时，可以采用分组排序。
```

```
7．逻辑处理接口：Reducer
	用户根据业务需求实现其中三个方法：reduce()   setup()   cleanup ()   
```

```
8．输出数据接口：OutputFormat
	（1）默认实现类是TextOutputFormat，功能逻辑是：将每一个KV对，向目标文本文件输出一行。
	（2）将SequenceFileOutputFormat输出作为后续 MapReduce任务的输入，这便是一种好的输出格式，因为它的格式紧凑，很容易被压缩。
	（3）用户还可以自定义OutputFormat。

```


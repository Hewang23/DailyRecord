### MapReduce工作流程

#### Map阶段

##### MapTask工作机制

```
（1）Read阶段：MapTask通过用户编写的RecordReader，从输入InputSplit中解析出一个个key/value。
（2）Map阶段：该节点主要是将解析出的key/value交给用户编写map()函数处理，并产生一系列新的key/value。
（3）Collect收集阶段：在用户编写map()函数中，当数据处理完成后，一般会调用OutputCollector.collect()输出结果。在该函数内部，它会将生成的key/value分区（调用Partitioner），并写入一个环形内存缓冲区中。
（4）Spill阶段：即“溢写”，当环形缓冲区满后，MapReduce会将数据写到本地磁盘上，生成一个临时文件。需要注意的是，将数据写入本地磁盘之前，先要对数据进行一次本地排序，并在必要时对数据进行合并、压缩等操作。
	溢写阶段详情：
	步骤1：利用快速排序算法对缓存区内的数据进行排序，排序方式是，先按照分区编号Partition进行排序，然后按照key进行排序。这样，经过排序后，数据以分区为单位聚集在一起，且同一分区内所有数据按照key有序。
	步骤2：按照分区编号由小到大依次将每个分区中的数据写入任务工作目录下的临时文件output/spillN.out（N表示当前溢写次数）中。如果用户设置了Combiner，则写入文件之前，对每个分区中的数据进行一次聚集操作。
	步骤3：将分区数据的元信息写到内存索引数据结构SpillRecord中，其中每个分区的元信息包括在临时文件中的偏移量、压缩前数据大小和压缩后数据大小。如果当前内存索引大小超过1MB，则将内存索引写到文件output/spillN.out.index中。
（5）Combine阶段：当所有数据处理完成后，MapTask对所有临时文件进行一次合并，以确保最终只会生成一个数据文件。
	当所有数据处理完后，MapTask会将所有临时文件合并成一个大文件，并保存到文件output/file.out中，同时生成相应的索引文件output/file.out.index。
	在进行文件合并过程中，MapTask以分区为单位进行合并。对于某个分区，它将采用多轮递归合并的方式。每轮合并io.sort.factor（默认10）个文件，并将产生的文件重新加入待合并列表中，对文件排序后，重复以上过程，直到最终得到一个大文件。
	让每个MapTask最终只生成一个数据文件，可避免同时打开大量文件和同时读取大量小文件产生的随机读取带来的开销。
```

#### —–> Shuffle机制

Map方法之后，Reduce之前的数据处理过程即为Shuffle（洗牌）；

##### Partion分区

```java
//默认分区
//自定义分区
```

##### WritableComparator排序

默认按照字典顺序排序，快排

```
Map阶段排序
Reduce阶段排序
```

排序分类

```
部分排序
全排序
辅助排序
自定义排序

```

##### Combiner合并

```
--MR中除Mapper和Reducer之外的组件，Reducer的子类
--同Reducer的区别在于运行的位置不同
	Combiner：每个MapTasker的节点运行
	Reducer：接收全局所有Mapper的输出结果
--即在于对每个MapTasker的输出进行局部汇总，减小网络传输量
--应用前提在于不能影响最终的业务逻辑
```

##### GroupingComparator分组

对Reduce阶段的数据根据某一个或几个字段进行分组

```

```

#### Reduce阶段

##### ReduceTask工作机制

```
（1）Copy阶段：ReduceTask从各个MapTask上远程拷贝一片数据，并针对某一片数据，如果其大小超过一定阈值，则写到磁盘上，否则直接放到内存中。
（2）Merge阶段：在远程拷贝数据的同时，ReduceTask启动了两个后台线程对内存和磁盘上的文件进行合并，以防止内存使用过多或磁盘上文件过多。
（3）Sort阶段：按照MapReduce语义，用户编写reduce()函数输入数据是按key进行聚集的一组数据。为了将key相同的数据聚在一起，Hadoop采用了基于排序的策略。由于各个MapTask已经实现对自己的处理结果进行了局部排序，因此，ReduceTask只需对所有数据进行一次归并排序即可。
（4）Reduce阶段：reduce()函数将计算结果写到HDFS上。
```

##### ReduceTask并行度

```
ReduceTask的并行度同样影响整个Job的执行并发度和执行效率，但与MapTask的并发数由切片数决定不同，ReduceTask数量的决定是可以直接手动设置：
job.setNumReduceTasks(4);
```

### 接口实现类：OutputFormat

```
TextOutputFormat类
	每条记录转换为文本行
SequenceFileOutputFormat类
	作为后续MapReduce任务的输入
自定义OutputFormat类
	控制最终文件的出输出路径和输出格式
	1）类继承
	2）改写RecoderWriter中输出数据方法write()
```


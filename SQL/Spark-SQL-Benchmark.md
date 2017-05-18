# Spark-SQL-Benchmark

此项目用于测试Spark SQL，分为三个测试模块，分别是在Basical，TPC-H，TPCDS。

其中Basical模块是测试Spark SQL的基本语法，数据集设计采用[Big Data Benchmark](https://amplab.cs.berkeley.edu/benchmark/)中的测试表设计，基本语法包括基本子句，函数和操作数。

[TPC-H](http://www.tpc.org/tpch/)和[TPC-DS](http://www.tpc.org/tpcds/)测试是事务性能管理委员会（TPC）发布的数据库评测基准，也是业界广泛应用的两款测试基准，其中TPC-H拥有8个表关系，22条查询语句，TPC-DS拥有24个表关系，99条SQL查询语句。

### 测试数据集生成

测试数据集生成分成三个模块

#### Basical SQL 模块

将项目打成jar包放到集群master节点中,然后修改/填写gen_basical_sql.py 脚本中的参数。

> ```
> ...
> jarname = [打包好的jar包名]
> datascale = [要生成的数据集规模]
> degree = [数据倾斜程度]
> hdfs = [数据在HDFS中输出路径]
> ...
> ```

最后执行gen_Basical_SQL.py脚本

> python gen_basical_sql.py

#### TPC-H 模块

1. 从官方下载地址将TPC-H测试基准工具下载。http://www.tpc.org/tpc_documents_current_versions/current_specifications.asp

2. 修改makefile文件。

   > cd dbgen
   >
   > cp makefile.suite makefile

   将makefile中的三个参数补全，这里的设置是

   > DATABASE = DB2
   >
   > MACHINE  = LINUX
   >
   > WORKLOAD = TPCH
   >
   > ...

3. make编译。

4. 将编译好得TPC-H工具和执行脚本发送到master和slave节点的相同目录下。执行脚本包括gen_per_node.py和gendata.py。其中，master节点需要gendata.py和host_list文件，host_list文件记录所有参与执行的slave节点的ip地址。slave节点需要gen_per_node.py脚本。

5. 运行master节点中的gendata.py脚本

   > python gendata.py \[scale_factor\] \[num_files\] \[host_list\] \[local_dir\] \[hdfs_path\]

   需要5个参数，

   > scale_factor = [数据规模]
   >
   > num_files  = [生成数据块数]
   >
   > host_list = [host_list文件]
   >
   > local_dir = [TPC-H工具本地目录]
   >
   > hdfs_path = [数据输出的HDFS地址]

#### TPC-DS模块

和TPC-H数据生成类似。

1. 从官方下载地址将TPC-DS测试基准工具下载。http://www.tpc.org/tpc_documents_current_versions/current_specifications.asp
2. 下载编译执行TPC-DS所需的所有环境，make编译。
3. ...



### 执行测试

将benchmark打成jar包放到master节点上，run_basical.py , run_tpch.py , run_tpcds.py三个脚本分别对应basical-sql模块，tpch模块和tpcds模块。

测试样例：

> python run_basical.py \[hdfs_path\] \[test_list] 

需要1个必要参数和1个可选参数

> hdfs_path = [数据输出的HDFS地址]
>
> test_list(可选) = [测试内容列表文件]，不填代表全部测试
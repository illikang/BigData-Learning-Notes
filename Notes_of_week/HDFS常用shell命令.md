# HDFS常用shell命令
## Hadoop Shell
HDFS提供了多种与之交互的方式，而命令行模式（Shell）就是其中最简洁，最被熟知的模式之一。

其实Hadoop的shell命令与Linux的文件操作命令非常类似，熟悉Linux的朋友完全可以触类旁通，只要看一下就能明白。即使不熟悉，也可以通过help命令获取信息：
```
hadoop fs -help [cmd]
```
另外，从hadoop 2.0以后，hdfs dfs命令和hadoop fs命令的效果是一样的。
## 常用命令列举
1. 查看根目录下文件：
```
hadoop fs -ls /
```
注意，hadoop上的文件的完整路径是：hdfs://localhost:9000(来自于core-site.xml配置文件)/,
写命令时可以省略前面的hdfs://localhost:9000，也可以使用home directory。

2. 创建目录：
```
hadoop fs -mkdir /leon  //在hdfs根目录下创建文件夹leon
hadoop fs -mkdir /test  //在hdfs根目录下创建文件夹test
hadoop fs -ls /
Found 2 items
drwxr-xr-x   - Leon supergroup          0 2018-12-01 23:05 /leon
drwxr-xr-x   - Leon supergroup          0 2018-12-02 00:16 /test
``` 
3. 删除目录：
```
hadoop fs -rm -r /test     //删除hdfs根目录下创建文件夹test
hadoop fs -ls /       
Found 1 items
drwxr-xr-x   - Leon supergroup          0 2018-12-01 23:05 /leon
```
4. 复制本地文件到HDFS：
```
hadoop fs -copyFromLocal ‪C:\hadoop-2.5.2\README.txt /
hadoop fs -ls /  
Found 2 items
-rw-r--r--   1 Leon supergroup       1366 2018-12-02 00:25 /README.txt
drwxr-xr-x   - Leon supergroup          0 2018-12-01 23:05 /leon
```
5. 复制本地文件夹到HDFS：
```
hadoop fs -put c:\logs /
hadoop fs -ls /
Found 3 items
-rw-r--r--   1 Leon supergroup       1366 2018-12-02 00:25 /README.txt
drwxr-xr-x   - Leon supergroup          0 2018-12-01 23:05 /leon
drwxr-xr-x   - Leon supergroup          0 2018-12-02 00:28 /logs
```
6. 查看HDFS上文本文件：
```
hadoop fs -cat /README.txt    //查看HDFS上根目录下的README.txt,以下输出为读取的内容片段
For the latest information about Hadoop, please visit our website at:

   http://hadoop.apache.org/core/
.....

```
7. 复制HDFS文件到本地：
```
hadoop fs -copyToLocal /README.txt c:\    //将HDFS根目录下的README.txt拷贝到本地C盘根目录
```

## 命令列表
```
Usage: hadoop fs [generic options]
        [-appendToFile <localsrc> ... <dst>]
        [-cat [-ignoreCrc] <src> ...]
        [-checksum <src> ...]
        [-chgrp [-R] GROUP PATH...]
        [-chmod [-R] <MODE[,MODE]... | OCTALMODE> PATH...]
        [-chown [-R] [OWNER][:[GROUP]] PATH...]
        [-copyFromLocal [-f] [-p] <localsrc> ... <dst>]
        [-copyToLocal [-p] [-ignoreCrc] [-crc] <src> ... <localdst>]
        [-count [-q] <path> ...]
        [-cp [-f] [-p | -p[topax]] <src> ... <dst>]
        [-createSnapshot <snapshotDir> [<snapshotName>]]
        [-deleteSnapshot <snapshotDir> <snapshotName>]
        [-df [-h] [<path> ...]]
        [-du [-s] [-h] <path> ...]
        [-expunge]
        [-get [-p] [-ignoreCrc] [-crc] <src> ... <localdst>]
        [-getfacl [-R] <path>]
        [-getfattr [-R] {-n name | -d} [-e en] <path>]
        [-getmerge [-nl] <src> <localdst>]
        [-help [cmd ...]]
        [-ls [-d] [-h] [-R] [<path> ...]]
        [-mkdir [-p] <path> ...]
        [-moveFromLocal <localsrc> ... <dst>]
        [-moveToLocal <src> <localdst>]
        [-mv <src> ... <dst>]
        [-put [-f] [-p] <localsrc> ... <dst>]
        [-renameSnapshot <snapshotDir> <oldName> <newName>]
        [-rm [-f] [-r|-R] [-skipTrash] <src> ...]
        [-rmdir [--ignore-fail-on-non-empty] <dir> ...]
        [-setfacl [-R] [{-b|-k} {-m|-x <acl_spec>} <path>]|[--set <acl_spec> <path>]]
        [-setfattr {-n name [-v value] | -x name} <path>]
        [-setrep [-R] [-w] <rep> <path> ...]
        [-stat [format] <path> ...]
        [-tail [-f] <file>]
        [-test -[defsz] <path>]
        [-text [-ignoreCrc] <src> ...]
        [-touchz <path> ...]
        [-usage [cmd ...]]
 ```

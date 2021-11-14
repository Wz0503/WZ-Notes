##### HDFS架构概述
* NameNode(mm):存储文件的元数据，如文件名、文件目录结构、文件属性（生成时间、副本数、文件权限），以及每个文件的块列表和块所在的DataNode等。
* DataNode(dn):在本地文件系统存储文件块数据，以及块数据的校验和。
* Secondary NameNode(2nn):每隔一段时间对NameNode元数据备份。

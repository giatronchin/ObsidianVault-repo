# Hadoop Command Line Interface    
Every command respect the following syntax:

    hdfs dfs <args>

For example a show current directory content command:
    
    hdfs dfs -ls 

*Path in FS*:  sono scritti in formato di URI

    scheme://authority/path

**Scheme**
* for HDFS is "hdfs";
* for the local filesystem is "file";

_Eg. Upload a file from local machin to the cluster_:

    hdfs dfs -cp
        file:///sampleData/spark/myfile.txt
        hdfs://rvm.svl.ibm.co:8020/user/spark/test/myfile.txt

Though scheme and authority are **optional** and the defaults can be defined within the **_core-site.xml_** configuration file [inside /conf directory of the Hadoop installation].

Some **POSIX-like** commands:

- cat, chgrp, chmod, chown, cp,  du, ls, mkdir, mv, rm, stat, tail

Some **HDFS-specif** commands:

- copyFromLocal(put), copyToLocal(get), get, getmerge, put, setrep

-w: to pause until the execution has stop

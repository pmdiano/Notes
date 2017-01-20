Run Hadoop locally:
```bash
export HADOOP_CLASSPATH=hadoop-examplex.jar
hadoop MaxTemperature input/ncdc/samples.txt output
```

View Hive settings:
```bash
hive -e 'SET -v'
```

Various XML files:

- Site files: `hive-site.xml`, `core-site.xml`, `hdfs-site.xml`, `mapred-site.xml`, `yarn-site.xml`
- Default files: `core-default.xml`, `hdfs-default.xml`, `mapred-default.xml`, `yarn-default.xml`

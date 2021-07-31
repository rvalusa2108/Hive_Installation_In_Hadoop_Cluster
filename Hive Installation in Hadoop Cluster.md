---


---

<p>In this page I am going to detail the steps required to install the Hive in the multi-node Hadoop cluster</p>
<p>Below are the steps involved in installing and configuring Hive in Hadoop cluster using MySql DB as Hive metastore.</p>
<ol>
<li>Download Hive Binary Package</li>
<li>Setup Hive Environment Variables</li>
<li>Setup Hive HDFS Folders in Hadoop FileSystem</li>
<li>Install MySQL Server</li>
<li>Configure Hive Metastore</li>
<li>Configure Hive File hive-site.xml</li>
<li>Initialize Hive Metastore Database Structure</li>
<li>Verify the Schema in MySQL CLI</li>
<li>Configure Hive API Authentication</li>
<li>Start HiveServer2 Service</li>
</ol>
<h2 id="download-hive-binary-package">Download Hive Binary Package</h2>
<p>Download Hive from Apache Hive official website and unpack it on the Hadoop master node.<br>
Make sure to download the compatible Hive version with the Hadoop version installed in the cluster</p>
<p>Check the Hadoop version installed in the cluster by running the below command.</p>
<pre><code>[hdpusr@masternode downloads]$ hdfs version
Hadoop 2.7.7
Subversion Unknown -r c1aad84bd27cd79c3d1a7dd58202a8c3ee1ed3ac
Compiled by stevel on 2018-07-18T22:47Z
Compiled with protoc 2.5.0
From source with checksum 792e15d20b12c74bd6f19a1fb886490
This command was run using /apps/hadoop-2.7.7/share/hadoop/common/hadoop-common-2.7.7.jar
</code></pre>
<p>For the Hadoop version 2.7.7 the latest compatible Hive version is 2.3.9<br>
<a href="https://www.strategylions.com.au/mirror/hive/hive-2.3.9/apache-hive-2.3.9-bin.tar.gz">https://www.strategylions.com.au/mirror/hive/hive-2.3.9/apache-hive-2.3.9-bin.tar.gz</a></p>
<pre><code>[hdpusr@masternode downloads]$ pwd
/apps/downloads
[hdpusr@masternode downloads]$ wget https://www.strategylions.com.au/mirror/hive/hive-2.3.9/apache-hive-2.3.9-bin.tar.gz
--2021-07-28 18:31:13--  https://www.strategylions.com.au/mirror/hive/hive-2.3.9/apache-hive-2.3.9-bin.tar.gz
Resolving www.strategylions.com.au (www.strategylions.com.au)... 172.67.141.54, 104.21.62.245, 2606:4700:3031::ac43:8d36, ...
Connecting to www.strategylions.com.au (www.strategylions.com.au)|172.67.141.54|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 286170958 (273M) [application/x-gzip]
Saving to: ‘apache-hive-2.3.9-bin.tar.gz’    
apache-hive-2.3.9-bin.tar.gz
100%[==================================================================================&gt;] 272.91M  3.22MB/s    in 87s    
2021-07-28 18:32:40 (3.15 MB/s) - ‘apache-hive-2.3.9-bin.tar.gz’ saved [286170958/286170958]
[hdpusr@masternode downloads]$ ls -ltrah
total 966M
-rw-r--r--. 1 root    root    125M Jul 13  2020 scala-2.12.12.rpm
-rw-r--r--. 1 hdpusr  hadoop  223M Sep  8  2020 spark-2.4.7-bin-hadoop2.7.tgz
-rw-rw-r--. 1 rvalusa rvalusa 137M Dec  2  2020 jdk-8u271-linux-x64.tar.gz
-rw-r--r--. 1 hdpusr  hadoop  209M Dec  2  2020 hadoop-2.7.7.tar.gz
drwxr-xr-x. 8 root    root     166 Jun  3 07:49 ..
-rw-r--r--. 1 hdpusr  hadoop  273M Jun 10 00:00 apache-hive-2.3.9-bin.tar.gz
drwxrwxrwx. 2 root    root     165 Jul 28 18:31 .
</code></pre>
<p>Unpack the Hive binary package into /apps folder</p>
<pre><code>[hdpusr@masternode downloads]$ pwd
/apps/downloads
[hdpusr@masternode downloads]$ ls -ltrah
total 966M
-rw-r--r--. 1 root    root    125M Jul 13  2020 scala-2.12.12.rpm
-rw-r--r--. 1 hdpusr  hadoop  223M Sep  8  2020 spark-2.4.7-bin-hadoop2.7.tgz
-rw-rw-r--. 1 rvalusa rvalusa 137M Dec  2  2020 jdk-8u271-linux-x64.tar.gz
-rw-r--r--. 1 hdpusr  hadoop  209M Dec  2  2020 hadoop-2.7.7.tar.gz
drwxr-xr-x. 8 hdpusr  hadoop   166 Jun  3 07:49 ..
-rw-r--r--. 1 hdpusr  hadoop  273M Jun 10 00:00 apache-hive-2.3.9-bin.tar.gz
drwxrwxrwx. 2 root    root     165 Jul 28 18:38 .
[hdpusr@masternode downloads]$ tar -xvzf apache-hive-2.3.9-bin.tar.gz -C /apps
apache-hive-2.3.9-bin/conf/hive-log4j2.properties.template
apache-hive-2.3.9-bin/conf/hive-exec-log4j2.properties.template
apache-hive-2.3.9-bin/conf/beeline-log4j2.properties.template
apache-hive-2.3.9-bin/conf/llap-daemon-log4j2.properties.template
apache-hive-2.3.9-bin/conf/llap-cli-log4j2.properties.template
apache-hive-2.3.9-bin/conf/parquet-logging.properties
apache-hive-2.3.9-bin/hcatalog/share/doc/hcatalog/README.txt
apache-hive-2.3.9-bin/lib/hive-common-2.3.9.jar
apache-hive-2.3.9-bin/lib/hive-shims-2.3.9.jar
apache-hive-2.3.9-bin/lib/hive-shims-common-2.3.9.jar
apache-hive-2.3.9-bin/lib/log4j-slf4j-impl-2.6.2.jar
apache-hive-2.3.9-bin/lib/log4j-api-2.6.2.jar
apache-hive-2.3.9-bin/lib/guava-14.0.1.jar
apache-hive-2.3.9-bin/lib/commons-lang-2.6.jar
apache-hive-2.3.9-bin/lib/libthrift-0.9.3.jar
apache-hive-2.3.9-bin/lib/httpclient-4.4.jar
apache-hive-2.3.9-bin/lib/httpcore-4.4.jar
apache-hive-2.3.9-bin/lib/commons-logging-1.2.jar
apache-hive-2.3.9-bin/lib/commons-codec-1.4.jar
apache-hive-2.3.9-bin/lib/curator-framework-2.7.1.jar
apache-hive-2.3.9-bin/lib/curator-client-2.7.1.jar
apache-hive-2.3.9-bin/lib/zookeeper-3.4.6.jar
apache-hive-2.3.9-bin/lib/jline-2.12.jar
apache-hive-2.3.9-bin/lib/netty-3.6.2.Final.jar
apache-hive-2.3.9-bin/lib/hive-shims-0.23-2.3.9.jar
apache-hive-2.3.9-bin/lib/guice-servlet-4.1.0.jar
apache-hive-2.3.9-bin/lib/javax.inject-1.jar
apache-hive-2.3.9-bin/lib/protobuf-java-2.5.0.jar
apache-hive-2.3.9-bin/lib/commons-io-2.4.jar
apache-hive-2.3.9-bin/lib/jettison-1.1.jar
apache-hive-2.3.9-bin/lib/activation-1.1.jar
apache-hive-2.3.9-bin/lib/jackson-core-asl-1.9.13.jar
apache-hive-2.3.9-bin/lib/jackson-mapper-asl-1.9.13.jar
apache-hive-2.3.9-bin/lib/jackson-jaxrs-1.9.13.jar
apache-hive-2.3.9-bin/lib/jackson-xc-1.9.13.jar
apache-hive-2.3.9-bin/lib/jersey-guice-1.19.jar
apache-hive-2.3.9-bin/lib/jersey-server-1.14.jar
apache-hive-2.3.9-bin/lib/asm-3.1.jar
apache-hive-2.3.9-bin/lib/commons-compress-1.9.jar
apache-hive-2.3.9-bin/lib/jetty-util-6.1.26.jar
apache-hive-2.3.9-bin/lib/jersey-client-1.9.jar
apache-hive-2.3.9-bin/lib/commons-cli-1.2.jar
apache-hive-2.3.9-bin/lib/commons-collections-3.2.2.jar
apache-hive-2.3.9-bin/lib/jetty-6.1.26.jar
apache-hive-2.3.9-bin/lib/hive-shims-scheduler-2.3.9.jar
apache-hive-2.3.9-bin/lib/hive-storage-api-2.4.0.jar
apache-hive-2.3.9-bin/lib/commons-lang3-3.1.jar
apache-hive-2.3.9-bin/lib/orc-core-1.3.4.jar
apache-hive-2.3.9-bin/lib/aircompressor-0.8.jar
apache-hive-2.3.9-bin/lib/slice-0.29.jar
apache-hive-2.3.9-bin/lib/jol-core-0.2.jar
apache-hive-2.3.9-bin/lib/jetty-all-7.6.0.v20120127.jar
apache-hive-2.3.9-bin/lib/geronimo-jta_1.1_spec-1.1.1.jar
apache-hive-2.3.9-bin/lib/mail-1.4.1.jar
apache-hive-2.3.9-bin/lib/geronimo-jaspic_1.0_spec-1.0.jar
apache-hive-2.3.9-bin/lib/geronimo-annotation_1.0_spec-1.1.1.jar
apache-hive-2.3.9-bin/lib/asm-commons-3.1.jar
apache-hive-2.3.9-bin/lib/asm-tree-3.1.jar
apache-hive-2.3.9-bin/lib/javax.servlet-3.0.0.v201112011016.jar
apache-hive-2.3.9-bin/lib/joda-time-2.8.1.jar
apache-hive-2.3.9-bin/lib/log4j-1.2-api-2.6.2.jar
apache-hive-2.3.9-bin/lib/log4j-core-2.6.2.jar
apache-hive-2.3.9-bin/lib/log4j-web-2.6.2.jar
apache-hive-2.3.9-bin/lib/ant-1.9.1.jar
apache-hive-2.3.9-bin/lib/ant-launcher-1.9.1.jar
apache-hive-2.3.9-bin/lib/json-1.8.jar
apache-hive-2.3.9-bin/lib/metrics-core-3.1.0.jar
apache-hive-2.3.9-bin/lib/metrics-jvm-3.1.0.jar
apache-hive-2.3.9-bin/lib/metrics-json-3.1.0.jar
apache-hive-2.3.9-bin/lib/jackson-databind-2.6.5.jar
apache-hive-2.3.9-bin/lib/jackson-annotations-2.6.0.jar
apache-hive-2.3.9-bin/lib/jackson-core-2.6.5.jar
apache-hive-2.3.9-bin/lib/dropwizard-metrics-hadoop-metrics2-reporter-0.1.2.jar
apache-hive-2.3.9-bin/lib/commons-math3-3.6.1.jar
apache-hive-2.3.9-bin/lib/commons-httpclient-3.1.jar
apache-hive-2.3.9-bin/lib/servlet-api-2.4.jar
apache-hive-2.3.9-bin/lib/jsp-api-2.1.jar
apache-hive-2.3.9-bin/lib/avro-1.8.2.jar
apache-hive-2.3.9-bin/lib/paranamer-2.7.jar
apache-hive-2.3.9-bin/lib/snappy-java-1.1.1.3.jar
apache-hive-2.3.9-bin/lib/xz-1.5.jar
apache-hive-2.3.9-bin/lib/gson-2.2.4.jar
apache-hive-2.3.9-bin/lib/curator-recipes-2.7.1.jar
apache-hive-2.3.9-bin/lib/jsr305-3.0.0.jar
apache-hive-2.3.9-bin/lib/htrace-core-3.1.0-incubating.jar
apache-hive-2.3.9-bin/lib/hive-serde-2.3.9.jar
apache-hive-2.3.9-bin/lib/hive-service-rpc-2.3.9.jar
apache-hive-2.3.9-bin/lib/jasper-compiler-5.5.23.jar
apache-hive-2.3.9-bin/lib/jsp-api-2.0.jar
apache-hive-2.3.9-bin/lib/ant-1.6.5.jar
apache-hive-2.3.9-bin/lib/jasper-runtime-5.5.23.jar
apache-hive-2.3.9-bin/lib/commons-el-1.0.jar
apache-hive-2.3.9-bin/lib/libfb303-0.9.3.jar
apache-hive-2.3.9-bin/lib/opencsv-2.3.jar
apache-hive-2.3.9-bin/lib/parquet-hadoop-bundle-1.8.1.jar
apache-hive-2.3.9-bin/lib/hive-metastore-2.3.9.jar
apache-hive-2.3.9-bin/lib/javolution-5.5.1.jar
apache-hive-2.3.9-bin/lib/hbase-client-1.1.1.jar
apache-hive-2.3.9-bin/lib/hbase-annotations-1.1.1.jar
apache-hive-2.3.9-bin/lib/findbugs-annotations-1.3.9-1.jar
apache-hive-2.3.9-bin/lib/hbase-common-1.1.1.jar
apache-hive-2.3.9-bin/lib/hbase-protocol-1.1.1.jar
apache-hive-2.3.9-bin/lib/netty-all-4.0.52.Final.jar
apache-hive-2.3.9-bin/lib/jcodings-1.0.8.jar
apache-hive-2.3.9-bin/lib/joni-2.1.2.jar
apache-hive-2.3.9-bin/lib/bonecp-0.8.0.RELEASE.jar
apache-hive-2.3.9-bin/lib/HikariCP-2.5.1.jar
apache-hive-2.3.9-bin/lib/derby-10.10.2.0.jar
apache-hive-2.3.9-bin/lib/datanucleus-api-jdo-4.2.4.jar
apache-hive-2.3.9-bin/lib/datanucleus-core-4.1.17.jar
apache-hive-2.3.9-bin/lib/datanucleus-rdbms-4.1.19.jar
apache-hive-2.3.9-bin/lib/commons-pool-1.5.4.jar
apache-hive-2.3.9-bin/lib/commons-dbcp-1.4.jar
apache-hive-2.3.9-bin/lib/jdo-api-3.0.1.jar
apache-hive-2.3.9-bin/lib/jta-1.1.jar
apache-hive-2.3.9-bin/lib/javax.jdo-3.2.0-m3.jar
apache-hive-2.3.9-bin/lib/transaction-api-1.1.jar
apache-hive-2.3.9-bin/lib/antlr-runtime-3.5.2.jar
apache-hive-2.3.9-bin/lib/hive-testutils-2.3.9.jar
apache-hive-2.3.9-bin/lib/tempus-fugit-1.1.jar
apache-hive-2.3.9-bin/lib/hive-exec-2.3.9.jar
apache-hive-2.3.9-bin/lib/hive-vector-code-gen-2.3.9.jar
apache-hive-2.3.9-bin/lib/velocity-1.5.jar
apache-hive-2.3.9-bin/lib/hive-llap-client-2.3.9.jar
apache-hive-2.3.9-bin/lib/hive-llap-common-2.3.9.jar
apache-hive-2.3.9-bin/lib/apache-curator-2.7.1.pom
apache-hive-2.3.9-bin/lib/hive-llap-tez-2.3.9.jar
apache-hive-2.3.9-bin/lib/spark-client-2.3.9.jar
apache-hive-2.3.9-bin/lib/kryo-shaded-3.0.3.jar
apache-hive-2.3.9-bin/lib/minlog-1.3.0.jar
apache-hive-2.3.9-bin/lib/objenesis-2.1.jar
apache-hive-2.3.9-bin/lib/spark-core_2.11-2.0.0.jar
apache-hive-2.3.9-bin/lib/avro-mapred-1.8.2-hadoop2.jar
apache-hive-2.3.9-bin/lib/avro-ipc-1.8.2.jar
apache-hive-2.3.9-bin/lib/chill_2.11-0.8.0.jar
apache-hive-2.3.9-bin/lib/scala-library-2.11.8.jar
apache-hive-2.3.9-bin/lib/chill-java-0.8.0.jar
apache-hive-2.3.9-bin/lib/xbean-asm5-shaded-4.4.jar
apache-hive-2.3.9-bin/lib/spark-launcher_2.11-2.0.0.jar
apache-hive-2.3.9-bin/lib/spark-tags_2.11-2.0.0.jar
apache-hive-2.3.9-bin/lib/scalatest_2.11-2.2.6.jar
apache-hive-2.3.9-bin/lib/scala-reflect-2.11.7.jar
apache-hive-2.3.9-bin/lib/scala-xml_2.11-1.0.2.jar
apache-hive-2.3.9-bin/lib/unused-1.0.0.jar
apache-hive-2.3.9-bin/lib/spark-network-common_2.11-2.0.0.jar
apache-hive-2.3.9-bin/lib/spark-network-shuffle_2.11-2.0.0.jar
apache-hive-2.3.9-bin/lib/spark-unsafe_2.11-2.0.0.jar
apache-hive-2.3.9-bin/lib/javax.servlet-api-3.1.0.jar
apache-hive-2.3.9-bin/lib/compress-lzf-1.0.3.jar
apache-hive-2.3.9-bin/lib/lz4-1.3.0.jar
apache-hive-2.3.9-bin/lib/RoaringBitmap-0.5.11.jar
apache-hive-2.3.9-bin/lib/json4s-jackson_2.11-3.2.11.jar
apache-hive-2.3.9-bin/lib/json4s-core_2.11-3.2.11.jar
apache-hive-2.3.9-bin/lib/json4s-ast_2.11-3.2.11.jar
apache-hive-2.3.9-bin/lib/scalap-2.11.0.jar
apache-hive-2.3.9-bin/lib/scala-compiler-2.11.0.jar
apache-hive-2.3.9-bin/lib/scala-parser-combinators_2.11-1.0.1.jar
apache-hive-2.3.9-bin/lib/mesos-0.21.1-shaded-protobuf.jar
apache-hive-2.3.9-bin/lib/stream-2.7.0.jar
apache-hive-2.3.9-bin/lib/metrics-graphite-3.1.2.jar
apache-hive-2.3.9-bin/lib/jackson-module-scala_2.11-2.6.5.jar
apache-hive-2.3.9-bin/lib/jackson-module-paranamer-2.6.5.jar
apache-hive-2.3.9-bin/lib/ivy-2.4.0.jar
apache-hive-2.3.9-bin/lib/pyrolite-4.9.jar
apache-hive-2.3.9-bin/lib/py4j-0.10.1.jar
apache-hive-2.3.9-bin/lib/ST4-4.0.4.jar
apache-hive-2.3.9-bin/lib/orc-tools-1.3.4.jar
apache-hive-2.3.9-bin/lib/groovy-all-2.4.4.jar
apache-hive-2.3.9-bin/lib/jodd-core-3.5.2.jar
apache-hive-2.3.9-bin/lib/avatica-1.8.0.jar
apache-hive-2.3.9-bin/lib/avatica-metrics-1.8.0.jar
apache-hive-2.3.9-bin/lib/eigenbase-properties-1.1.5.jar
apache-hive-2.3.9-bin/lib/janino-2.7.6.jar
apache-hive-2.3.9-bin/lib/commons-compiler-2.7.6.jar
apache-hive-2.3.9-bin/lib/pentaho-aggdesigner-algorithm-5.1.5-jhyde.jar
apache-hive-2.3.9-bin/lib/JavaEWAH-0.3.2.jar
apache-hive-2.3.9-bin/lib/stax-api-1.0.1.jar
apache-hive-2.3.9-bin/lib/hive-service-2.3.9.jar
apache-hive-2.3.9-bin/lib/hive-llap-server-2.3.9.jar
apache-hive-2.3.9-bin/lib/slider-core-0.90.2-incubating.jar
apache-hive-2.3.9-bin/lib/jcommander-1.32.jar
apache-hive-2.3.9-bin/lib/hive-llap-common-2.3.9-tests.jar
apache-hive-2.3.9-bin/lib/hbase-hadoop2-compat-1.1.1.jar
apache-hive-2.3.9-bin/lib/hbase-hadoop-compat-1.1.1.jar
apache-hive-2.3.9-bin/lib/commons-math-2.2.jar
apache-hive-2.3.9-bin/lib/metrics-core-2.2.0.jar
apache-hive-2.3.9-bin/lib/hbase-server-1.1.1.jar
apache-hive-2.3.9-bin/lib/hbase-procedure-1.1.1.jar
apache-hive-2.3.9-bin/lib/hbase-common-1.1.1-tests.jar
apache-hive-2.3.9-bin/lib/hbase-prefix-tree-1.1.1.jar
apache-hive-2.3.9-bin/lib/jetty-sslengine-6.1.26.jar
apache-hive-2.3.9-bin/lib/jsp-2.1-6.1.14.jar
apache-hive-2.3.9-bin/lib/jsp-api-2.1-6.1.14.jar
apache-hive-2.3.9-bin/lib/servlet-api-2.5-6.1.14.jar
apache-hive-2.3.9-bin/lib/jamon-runtime-2.3.1.jar
apache-hive-2.3.9-bin/lib/disruptor-3.3.0.jar
apache-hive-2.3.9-bin/lib/jpam-1.1.jar
apache-hive-2.3.9-bin/lib/hive-jdbc-2.3.9.jar
apache-hive-2.3.9-bin/lib/hive-beeline-2.3.9.jar
apache-hive-2.3.9-bin/lib/super-csv-2.2.0.jar
apache-hive-2.3.9-bin/lib/hive-cli-2.3.9.jar
apache-hive-2.3.9-bin/lib/hive-contrib-2.3.9.jar
apache-hive-2.3.9-bin/lib/hive-hbase-handler-2.3.9.jar
apache-hive-2.3.9-bin/lib/hbase-hadoop2-compat-1.1.1-tests.jar
apache-hive-2.3.9-bin/lib/hive-druid-handler-2.3.9.jar
apache-hive-2.3.9-bin/lib/java-util-0.27.10.jar
apache-hive-2.3.9-bin/lib/config-magic-0.9.jar
apache-hive-2.3.9-bin/lib/rhino-1.7R5.jar
apache-hive-2.3.9-bin/lib/json-path-2.1.0.jar
apache-hive-2.3.9-bin/lib/druid-server-0.9.2.jar
apache-hive-2.3.9-bin/lib/druid-processing-0.9.2.jar
apache-hive-2.3.9-bin/lib/druid-common-0.9.2.jar
apache-hive-2.3.9-bin/lib/druid-api-0.9.2.jar
apache-hive-2.3.9-bin/lib/guice-multibindings-4.1.0.jar
apache-hive-2.3.9-bin/lib/airline-0.7.jar
apache-hive-2.3.9-bin/lib/jackson-dataformat-smile-2.4.6.jar
apache-hive-2.3.9-bin/lib/hibernate-validator-5.1.3.Final.jar
apache-hive-2.3.9-bin/lib/validation-api-1.1.0.Final.jar
apache-hive-2.3.9-bin/lib/jboss-logging-3.1.3.GA.jar
apache-hive-2.3.9-bin/lib/classmate-1.0.0.jar
apache-hive-2.3.9-bin/lib/commons-dbcp2-2.0.1.jar
apache-hive-2.3.9-bin/lib/commons-pool2-2.2.jar
apache-hive-2.3.9-bin/lib/javax.el-api-3.0.0.jar
apache-hive-2.3.9-bin/lib/jackson-datatype-guava-2.4.6.jar
apache-hive-2.3.9-bin/lib/jackson-datatype-joda-2.4.6.jar
apache-hive-2.3.9-bin/lib/jdbi-2.63.1.jar
apache-hive-2.3.9-bin/lib/log4j-jul-2.5.jar
apache-hive-2.3.9-bin/lib/antlr4-runtime-4.5.jar
apache-hive-2.3.9-bin/lib/bytebuffer-collections-0.2.5.jar
apache-hive-2.3.9-bin/lib/extendedset-1.3.10.jar
apache-hive-2.3.9-bin/lib/emitter-0.3.6.jar
apache-hive-2.3.9-bin/lib/http-client-1.0.4.jar
apache-hive-2.3.9-bin/lib/server-metrics-0.2.8.jar
apache-hive-2.3.9-bin/lib/icu4j-4.8.1.jar
apache-hive-2.3.9-bin/lib/mapdb-1.0.8.jar
apache-hive-2.3.9-bin/lib/druid-console-0.0.2.jar
apache-hive-2.3.9-bin/lib/javax.el-3.0.0.jar
apache-hive-2.3.9-bin/lib/curator-x-discovery-2.11.0.jar
apache-hive-2.3.9-bin/lib/jackson-jaxrs-json-provider-2.4.6.jar
apache-hive-2.3.9-bin/lib/jackson-jaxrs-base-2.4.6.jar
apache-hive-2.3.9-bin/lib/jackson-module-jaxb-annotations-2.4.6.jar
apache-hive-2.3.9-bin/lib/jackson-jaxrs-smile-provider-2.4.6.jar
apache-hive-2.3.9-bin/lib/jetty-server-9.2.5.v20141112.jar
apache-hive-2.3.9-bin/lib/jetty-http-9.2.5.v20141112.jar
apache-hive-2.3.9-bin/lib/jetty-util-9.2.5.v20141112.jar
apache-hive-2.3.9-bin/lib/jetty-io-9.2.5.v20141112.jar
apache-hive-2.3.9-bin/lib/jetty-proxy-9.2.5.v20141112.jar
apache-hive-2.3.9-bin/lib/jetty-client-9.2.5.v20141112.jar
apache-hive-2.3.9-bin/lib/tesla-aether-0.0.5.jar
apache-hive-2.3.9-bin/lib/aether-api-0.9.0.M2.jar
apache-hive-2.3.9-bin/lib/aether-spi-0.9.0.M2.jar
apache-hive-2.3.9-bin/lib/aether-util-0.9.0.M2.jar
apache-hive-2.3.9-bin/lib/aether-impl-0.9.0.M2.jar
apache-hive-2.3.9-bin/lib/aether-connector-file-0.9.0.M2.jar
apache-hive-2.3.9-bin/lib/aether-connector-okhttp-0.0.9.jar
apache-hive-2.3.9-bin/lib/okhttp-1.0.2.jar
apache-hive-2.3.9-bin/lib/wagon-provider-api-2.4.jar
apache-hive-2.3.9-bin/lib/plexus-utils-3.0.15.jar
apache-hive-2.3.9-bin/lib/maven-aether-provider-3.1.1.jar
apache-hive-2.3.9-bin/lib/maven-model-3.1.1.jar
apache-hive-2.3.9-bin/lib/maven-model-builder-3.1.1.jar
apache-hive-2.3.9-bin/lib/plexus-interpolation-1.19.jar
apache-hive-2.3.9-bin/lib/maven-repository-metadata-3.1.1.jar
apache-hive-2.3.9-bin/lib/maven-settings-builder-3.1.1.jar
apache-hive-2.3.9-bin/lib/maven-settings-3.1.1.jar
apache-hive-2.3.9-bin/lib/spymemcached-2.11.7.jar
apache-hive-2.3.9-bin/lib/jetty-servlet-9.2.5.v20141112.jar
apache-hive-2.3.9-bin/lib/jetty-security-9.2.5.v20141112.jar
apache-hive-2.3.9-bin/lib/jetty-servlets-9.2.5.v20141112.jar
apache-hive-2.3.9-bin/lib/jetty-continuation-9.2.5.v20141112.jar
apache-hive-2.3.9-bin/lib/irc-api-1.0-0014.jar
apache-hive-2.3.9-bin/lib/geoip2-0.4.0.jar
apache-hive-2.3.9-bin/lib/maxminddb-0.2.0.jar
apache-hive-2.3.9-bin/lib/google-http-client-jackson2-1.15.0-rc.jar
apache-hive-2.3.9-bin/lib/derbynet-10.11.1.1.jar
apache-hive-2.3.9-bin/lib/derbyclient-10.11.1.1.jar
apache-hive-2.3.9-bin/lib/druid-hdfs-storage-0.9.2.jar
apache-hive-2.3.9-bin/lib/mysql-metadata-storage-0.9.2.jar
apache-hive-2.3.9-bin/lib/postgresql-metadata-storage-0.9.2.jar
apache-hive-2.3.9-bin/lib/postgresql-9.4.1208.jre7.jar
apache-hive-2.3.9-bin/lib/hive-jdbc-handler-2.3.9.jar
apache-hive-2.3.9-bin/lib/hive-accumulo-handler-2.3.9.jar
apache-hive-2.3.9-bin/lib/accumulo-core-1.7.3.jar
apache-hive-2.3.9-bin/lib/accumulo-fate-1.7.3.jar
apache-hive-2.3.9-bin/lib/accumulo-start-1.7.3.jar
apache-hive-2.3.9-bin/lib/commons-vfs2-2.1.jar
apache-hive-2.3.9-bin/lib/accumulo-trace-1.7.3.jar
apache-hive-2.3.9-bin/lib/hive-llap-ext-client-2.3.9.jar
apache-hive-2.3.9-bin/lib/hive-hplsql-2.3.9.jar
apache-hive-2.3.9-bin/lib/org.abego.treelayout.core-1.0.1.jar
apache-hive-2.3.9-bin/jdbc/hive-jdbc-2.3.9-standalone.jar
apache-hive-2.3.9-bin/lib/hive-hcatalog-core-2.3.9.jar
apache-hive-2.3.9-bin/lib/hive-hcatalog-server-extensions-2.3.9.jar
apache-hive-2.3.9-bin/hcatalog/share/hcatalog/hive-hcatalog-streaming-2.3.9.jar
apache-hive-2.3.9-bin/hcatalog/share/hcatalog/hive-hcatalog-core-2.3.9.jar
apache-hive-2.3.9-bin/hcatalog/share/hcatalog/hive-hcatalog-pig-adapter-2.3.9.jar
apache-hive-2.3.9-bin/hcatalog/share/hcatalog/hive-hcatalog-server-extensions-2.3.9.jar
apache-hive-2.3.9-bin/hcatalog/share/webhcat/svr/lib/jersey-json-1.14.jar
apache-hive-2.3.9-bin/hcatalog/share/webhcat/svr/lib/jaxb-impl-2.2.3-1.jar
apache-hive-2.3.9-bin/hcatalog/share/webhcat/svr/lib/jersey-core-1.14.jar
apache-hive-2.3.9-bin/hcatalog/share/webhcat/svr/lib/jul-to-slf4j-1.7.10.jar
apache-hive-2.3.9-bin/hcatalog/share/webhcat/svr/lib/jersey-servlet-1.14.jar
apache-hive-2.3.9-bin/hcatalog/share/webhcat/svr/lib/hive-webhcat-2.3.9.jar
apache-hive-2.3.9-bin/hcatalog/share/webhcat/svr/lib/wadl-resourcedoc-doclet-1.4.jar
apache-hive-2.3.9-bin/hcatalog/share/webhcat/svr/lib/commons-exec-1.1.jar
apache-hive-2.3.9-bin/hcatalog/share/webhcat/svr/lib/jetty-all-server-7.6.0.v20120127.jar
apache-hive-2.3.9-bin/hcatalog/share/webhcat/java-client/hive-webhcat-java-client-2.3.9.jar
apache-hive-2.3.9-bin/LICENSE
apache-hive-2.3.9-bin/RELEASE_NOTES.txt
apache-hive-2.3.9-bin/NOTICE
apache-hive-2.3.9-bin/binary-package-licenses/com.thoughtworks.paranamer-LICENSE
apache-hive-2.3.9-bin/binary-package-licenses/org.codehaus.janino-LICENSE
apache-hive-2.3.9-bin/binary-package-licenses/org.jamon.jamon-runtime-LICENSE
apache-hive-2.3.9-bin/binary-package-licenses/org.mozilla.rhino-LICENSE
apache-hive-2.3.9-bin/binary-package-licenses/org.jruby-LICENSE
apache-hive-2.3.9-bin/binary-package-licenses/jline-LICENSE
apache-hive-2.3.9-bin/binary-package-licenses/org.antlr-LICENSE
apache-hive-2.3.9-bin/binary-package-licenses/org.abego.treelayout.core-LICENSE
apache-hive-2.3.9-bin/binary-package-licenses/sqlline-LICENSE
apache-hive-2.3.9-bin/binary-package-licenses/org.slf4j-LICENSE
apache-hive-2.3.9-bin/binary-package-licenses/org.antlr.antlr4-LICENSE
apache-hive-2.3.9-bin/binary-package-licenses/com.google.protobuf-LICENSE
apache-hive-2.3.9-bin/binary-package-licenses/com.sun.jersey-LICENSE
apache-hive-2.3.9-bin/binary-package-licenses/javolution-LICENSE
apache-hive-2.3.9-bin/binary-package-licenses/NOTICE
apache-hive-2.3.9-bin/binary-package-licenses/asm-LICENSE
apache-hive-2.3.9-bin/binary-package-licenses/com.ibm.icu.icu4j-LICENSE
apache-hive-2.3.9-bin/binary-package-licenses/org.antlr.stringtemplate-LICENSE
apache-hive-2.3.9-bin/binary-package-licenses/javax.transaction.transaction-api-LICENSE
apache-hive-2.3.9-bin/examples/files/hive_626_bar.txt
apache-hive-2.3.9-bin/examples/files/docurl.txt
apache-hive-2.3.9-bin/examples/files/tjoin2.txt
apache-hive-2.3.9-bin/examples/files/person+age.txt
apache-hive-2.3.9-bin/examples/files/avro_charvarchar.txt
apache-hive-2.3.9-bin/examples/files/parquet_array_null_element.txt
apache-hive-2.3.9-bin/examples/files/smbbucket_3.rc
apache-hive-2.3.9-bin/examples/files/tiny_a.txt
apache-hive-2.3.9-bin/examples/files/parquet_type_promotion.txt
apache-hive-2.3.9-bin/examples/files/over10k.gz
apache-hive-2.3.9-bin/examples/files/map_table.txt
apache-hive-2.3.9-bin/examples/files/table1.avsc
apache-hive-2.3.9-bin/examples/files/in9.txt
apache-hive-2.3.9-bin/examples/files/tjoin1.txt
apache-hive-2.3.9-bin/examples/files/sortdp.txt
apache-hive-2.3.9-bin/examples/files/tsformat.json
apache-hive-2.3.9-bin/examples/files/sample-queryplan-in-history.txt
apache-hive-2.3.9-bin/examples/files/tiny_b.txt
apache-hive-2.3.9-bin/examples/files/non_ascii_tbl.txt
apache-hive-2.3.9-bin/examples/files/in8.txt
apache-hive-2.3.9-bin/examples/files/dec_old.avro
apache-hive-2.3.9-bin/examples/files/fact-data.txt
apache-hive-2.3.9-bin/examples/files/customer_address.txt
apache-hive-2.3.9-bin/examples/files/create_nested_type.txt
apache-hive-2.3.9-bin/examples/files/kv8.txt
apache-hive-2.3.9-bin/examples/files/part.rc
apache-hive-2.3.9-bin/examples/files/flights_tiny.txt.1
apache-hive-2.3.9-bin/examples/files/kv1.string-sorted.txt
apache-hive-2.3.9-bin/examples/files/encoding-utf8.txt
apache-hive-2.3.9-bin/examples/files/test1.txt
apache-hive-2.3.9-bin/examples/files/emp2.txt
apache-hive-2.3.9-bin/examples/files/dynpartdata1.txt
apache-hive-2.3.9-bin/examples/files/sample-queryplan.txt
apache-hive-2.3.9-bin/examples/files/nestedcomplex_additional.txt
apache-hive-2.3.9-bin/examples/files/kv9.txt
apache-hive-2.3.9-bin/examples/files/ct_events_clean.txt
apache-hive-2.3.9-bin/examples/files/encoding_iso-8859-1.txt
apache-hive-2.3.9-bin/examples/files/orc_create.txt
apache-hive-2.3.9-bin/examples/files/grouping_sets.txt
apache-hive-2.3.9-bin/examples/files/hive_626_foo.txt
apache-hive-2.3.9-bin/examples/files/dec.txt
apache-hive-2.3.9-bin/examples/files/tbl.txt
apache-hive-2.3.9-bin/examples/files/apache.access.log
apache-hive-2.3.9-bin/examples/files/dynpartdata2.txt
apache-hive-2.3.9-bin/examples/files/smbbucket_2.rc
apache-hive-2.3.9-bin/examples/files/parquet_create.txt
apache-hive-2.3.9-bin/examples/files/smbdata.txt
apache-hive-2.3.9-bin/examples/files/employee2.dat
apache-hive-2.3.9-bin/examples/files/pw17.txt
apache-hive-2.3.9-bin/examples/files/grad.avsc
apache-hive-2.3.9-bin/examples/files/orc_split_elim.orc
apache-hive-2.3.9-bin/examples/files/UserVisits.dat
apache-hive-2.3.9-bin/examples/files/nullfile.txt
apache-hive-2.3.9-bin/examples/files/smbbucket_3.txt
apache-hive-2.3.9-bin/examples/files/dynamic_partition_insert.txt
apache-hive-2.3.9-bin/examples/files/decimal_10_0.txt
apache-hive-2.3.9-bin/examples/files/map_null_schema.avro
apache-hive-2.3.9-bin/examples/files/doctors.avro
apache-hive-2.3.9-bin/examples/files/windowing_distinct.txt
apache-hive-2.3.9-bin/examples/files/type_evolution.avro
apache-hive-2.3.9-bin/examples/files/empty2.txt
apache-hive-2.3.9-bin/examples/files/dynpart_test.txt
apache-hive-2.3.9-bin/examples/files/T1.txt
apache-hive-2.3.9-bin/examples/files/in_file.dat
apache-hive-2.3.9-bin/examples/files/extrapolate_stats_partial_ndv.txt
apache-hive-2.3.9-bin/examples/files/agg_01-p1.txt
apache-hive-2.3.9-bin/examples/files/smbbucket_2.txt
apache-hive-2.3.9-bin/examples/files/symlink2.txt
apache-hive-2.3.9-bin/examples/files/SingleFieldGroupInList.parquet
apache-hive-2.3.9-bin/examples/files/decimal_1_1.txt
apache-hive-2.3.9-bin/examples/files/map_null_val.avro
apache-hive-2.3.9-bin/examples/files/agg_01-p3.txt
apache-hive-2.3.9-bin/examples/files/ThriftSingleFieldGroupInList.parquet
apache-hive-2.3.9-bin/examples/files/test2.dat
apache-hive-2.3.9-bin/examples/files/dept.txt
apache-hive-2.3.9-bin/examples/files/lt100.txt.deflate
apache-hive-2.3.9-bin/examples/files/extrapolate_stats_partial.txt
apache-hive-2.3.9-bin/examples/files/T3.txt
apache-hive-2.3.9-bin/examples/files/empty1.txt
apache-hive-2.3.9-bin/examples/files/T2.txt
apache-hive-2.3.9-bin/examples/files/ProxyAuth.res
apache-hive-2.3.9-bin/examples/files/3col_data.txt
apache-hive-2.3.9-bin/examples/files/regex-path-2015121003.txt
apache-hive-2.3.9-bin/examples/files/agg_01-p2.txt
apache-hive-2.3.9-bin/examples/files/smbbucket_1.txt
apache-hive-2.3.9-bin/examples/files/ThriftPrimitiveInList.parquet
apache-hive-2.3.9-bin/examples/files/symlink1.txt
apache-hive-2.3.9-bin/examples/files/covar_tab.txt
apache-hive-2.3.9-bin/examples/files/lt100.txt
apache-hive-2.3.9-bin/examples/files/StringMapOfOptionalIntArray.parquet
apache-hive-2.3.9-bin/examples/files/orc_create_people.txt
apache-hive-2.3.9-bin/examples/files/smallsrcsortbucket1outof4.txt
apache-hive-2.3.9-bin/examples/files/kv1_broken.seq
apache-hive-2.3.9-bin/examples/files/regex-path-2015-12-10_03.txt
apache-hive-2.3.9-bin/examples/files/kv1kv2.cogroup.txt
apache-hive-2.3.9-bin/examples/files/array_table.txt
apache-hive-2.3.9-bin/examples/files/z.txt
apache-hive-2.3.9-bin/examples/files/sales.txt
apache-hive-2.3.9-bin/examples/files/smb_bucket_input.txt
apache-hive-2.3.9-bin/examples/files/hive_626_count.txt
apache-hive-2.3.9-bin/examples/files/double.txt
apache-hive-2.3.9-bin/examples/files/lt100.sorted.txt
apache-hive-2.3.9-bin/examples/files/futurama_episodes.avro
apache-hive-2.3.9-bin/examples/files/x.txt
apache-hive-2.3.9-bin/examples/files/dec.avro
apache-hive-2.3.9-bin/examples/files/keystore_exampledotcom.jks
apache-hive-2.3.9-bin/examples/files/bool.txt
apache-hive-2.3.9-bin/examples/files/char_varchar_udf.txt
apache-hive-2.3.9-bin/examples/files/y.txt
apache-hive-2.3.9-bin/examples/files/store.txt
apache-hive-2.3.9-bin/examples/files/srcsortbucket1outof4.txt
apache-hive-2.3.9-bin/examples/files/extrapolate_stats_full.txt
apache-hive-2.3.9-bin/examples/files/complex.seq
apache-hive-2.3.9-bin/examples/files/json.txt
apache-hive-2.3.9-bin/examples/files/source.txt
apache-hive-2.3.9-bin/examples/files/smallsrcsortbucket4outof4.txt
apache-hive-2.3.9-bin/examples/files/kv1_cc.txt
apache-hive-2.3.9-bin/examples/files/sour2.txt
apache-hive-2.3.9-bin/examples/files/keystore.jks
apache-hive-2.3.9-bin/examples/files/UnannotatedListOfPrimitives.parquet
apache-hive-2.3.9-bin/examples/files/parquet_non_dictionary_types.txt
apache-hive-2.3.9-bin/examples/files/v1.txt
apache-hive-2.3.9-bin/examples/files/input.txt
apache-hive-2.3.9-bin/examples/files/grouping_sets1.txt
apache-hive-2.3.9-bin/examples/files/employee_part.txt
apache-hive-2.3.9-bin/examples/files/AvroPrimitiveInList.parquet
apache-hive-2.3.9-bin/examples/files/apache.access.2.log
apache-hive-2.3.9-bin/examples/files/avro_date.txt
apache-hive-2.3.9-bin/examples/files/episodes.avro
apache-hive-2.3.9-bin/examples/files/part_tiny_nulls.txt
apache-hive-2.3.9-bin/examples/files/kv1_cb.txt
apache-hive-2.3.9-bin/examples/files/sour1.txt
apache-hive-2.3.9-bin/examples/files/NestedMap.parquet
apache-hive-2.3.9-bin/examples/files/table1_1.avsc
apache-hive-2.3.9-bin/examples/files/escape_crlf.txt
apache-hive-2.3.9-bin/examples/files/HiveGroup.parquet
apache-hive-2.3.9-bin/examples/files/timestamps.txt
apache-hive-2.3.9-bin/examples/files/part_tiny.txt
apache-hive-2.3.9-bin/examples/files/srcbucket1.txt
apache-hive-2.3.9-bin/examples/files/v2.txt
apache-hive-2.3.9-bin/examples/files/srcbucket0.txt
apache-hive-2.3.9-bin/examples/files/grouping_sets2.txt
apache-hive-2.3.9-bin/examples/files/srcsortbucket4outof4.txt
apache-hive-2.3.9-bin/examples/files/kv1.seq
apache-hive-2.3.9-bin/examples/files/SortDescCol1Col2.txt
apache-hive-2.3.9-bin/examples/files/primitive_type_arrays.txt
apache-hive-2.3.9-bin/examples/files/union_input.txt
apache-hive-2.3.9-bin/examples/files/text-en.txt
apache-hive-2.3.9-bin/examples/files/customers.txt
apache-hive-2.3.9-bin/examples/files/test.dat
apache-hive-2.3.9-bin/examples/files/dec_comp.txt
apache-hive-2.3.9-bin/examples/files/nested_complex.txt
apache-hive-2.3.9-bin/examples/files/escapetest.txt
apache-hive-2.3.9-bin/examples/files/opencsv-data.txt
apache-hive-2.3.9-bin/examples/files/parquet_partitioned.txt
apache-hive-2.3.9-bin/examples/files/csv.txt
apache-hive-2.3.9-bin/examples/files/groupby_groupingid.txt
apache-hive-2.3.9-bin/examples/files/sample.json
apache-hive-2.3.9-bin/examples/files/sample2.json
apache-hive-2.3.9-bin/examples/files/smallsrcsortbucket2outof4.txt
apache-hive-2.3.9-bin/examples/files/posexplode_data.txt
apache-hive-2.3.9-bin/examples/files/loc.txt
apache-hive-2.3.9-bin/examples/files/srcsortbucket2outof4.txt
apache-hive-2.3.9-bin/examples/files/employee.dat
apache-hive-2.3.9-bin/examples/files/service_request_clean.txt
apache-hive-2.3.9-bin/examples/files/NewRequiredGroupInList.parquet
apache-hive-2.3.9-bin/examples/files/dim_shops.txt
apache-hive-2.3.9-bin/examples/files/orders.txt
apache-hive-2.3.9-bin/examples/files/decimal.txt
apache-hive-2.3.9-bin/examples/files/parquet_external_time.parq
apache-hive-2.3.9-bin/examples/files/infer_const_type.txt
apache-hive-2.3.9-bin/examples/files/dec.parq
apache-hive-2.3.9-bin/examples/files/SortCol2Col1.txt
apache-hive-2.3.9-bin/examples/files/dim-data.txt
apache-hive-2.3.9-bin/examples/files/person age.txt
apache-hive-2.3.9-bin/examples/files/ts_formats.txt
apache-hive-2.3.9-bin/examples/files/cbo_t4.txt
apache-hive-2.3.9-bin/examples/files/nested_orders.txt
apache-hive-2.3.9-bin/examples/files/avro_timestamp.txt
apache-hive-2.3.9-bin/examples/files/truststore.jks
apache-hive-2.3.9-bin/examples/files/in3.txt
apache-hive-2.3.9-bin/examples/files/kv7.txt
apache-hive-2.3.9-bin/examples/files/unique_1.txt
apache-hive-2.3.9-bin/examples/files/parquet_types.txt
apache-hive-2.3.9-bin/examples/files/data_with_escape.txt
apache-hive-2.3.9-bin/examples/files/alltypes2.txt
apache-hive-2.3.9-bin/examples/files/flights_tiny.txt
apache-hive-2.3.9-bin/examples/files/vc1.txt
apache-hive-2.3.9-bin/examples/files/NewOptionalGroupInList.parquet
apache-hive-2.3.9-bin/examples/files/leftsemijoin_mr_t1.txt
apache-hive-2.3.9-bin/examples/files/parquet_columnar.txt
apache-hive-2.3.9-bin/examples/files/kv6.txt
apache-hive-2.3.9-bin/examples/files/in2.txt
apache-hive-2.3.9-bin/examples/files/store_sales.txt
apache-hive-2.3.9-bin/examples/files/cbo_t5.txt
apache-hive-2.3.9-bin/examples/files/MultiFieldGroupInList.parquet
apache-hive-2.3.9-bin/examples/files/emp.txt
apache-hive-2.3.9-bin/examples/files/part.seq
apache-hive-2.3.9-bin/examples/files/kv4.txt
apache-hive-2.3.9-bin/examples/files/unique_2.txt
apache-hive-2.3.9-bin/examples/files/mapNull.txt
apache-hive-2.3.9-bin/examples/files/flights_join.txt
apache-hive-2.3.9-bin/examples/files/things2.txt
apache-hive-2.3.9-bin/examples/files/smbbucket_1.rc
apache-hive-2.3.9-bin/examples/files/null.txt
apache-hive-2.3.9-bin/examples/files/SortDescCol2Col1.txt
apache-hive-2.3.9-bin/examples/files/leftsemijoin_mr_t2.txt
apache-hive-2.3.9-bin/examples/files/kv5.txt
apache-hive-2.3.9-bin/examples/files/in1.txt
apache-hive-2.3.9-bin/examples/files/cbo_t6.txt
apache-hive-2.3.9-bin/examples/files/regex-path-201512-10_03.txt
apache-hive-2.3.9-bin/examples/files/cbo_t2.txt
apache-hive-2.3.9-bin/examples/files/in5.txt
apache-hive-2.3.9-bin/examples/files/kv1.txt
apache-hive-2.3.9-bin/examples/files/int.txt
apache-hive-2.3.9-bin/examples/files/HiveRequiredGroupInList.parquet
apache-hive-2.3.9-bin/examples/files/archive_corrupt.rc
apache-hive-2.3.9-bin/examples/files/lineitem.txt
apache-hive-2.3.9-bin/examples/files/kv1.val.sorted.txt
apache-hive-2.3.9-bin/examples/files/parquet_create_people.txt
apache-hive-2.3.9-bin/examples/files/small_csv.csv
apache-hive-2.3.9-bin/examples/files/srcbucket21.txt
apache-hive-2.3.9-bin/examples/files/kv10.txt
apache-hive-2.3.9-bin/examples/files/srcbucket20.txt
apache-hive-2.3.9-bin/examples/files/in4.txt
apache-hive-2.3.9-bin/examples/files/smb_bucket_input.rc
apache-hive-2.3.9-bin/examples/files/smallsrcsortbucket3outof4.txt
apache-hive-2.3.9-bin/examples/files/datatypes.txt
apache-hive-2.3.9-bin/examples/files/symlink-with-regex.txt
apache-hive-2.3.9-bin/examples/files/cbo_t3.txt
apache-hive-2.3.9-bin/examples/files/cbo_t1.txt
apache-hive-2.3.9-bin/examples/files/trunc_number.txt
apache-hive-2.3.9-bin/examples/files/in6.txt
apache-hive-2.3.9-bin/examples/files/kv2.txt
apache-hive-2.3.9-bin/examples/files/UnannotatedListOfGroups.parquet
apache-hive-2.3.9-bin/examples/files/AvroSingleFieldGroupInList.parquet
apache-hive-2.3.9-bin/examples/files/srcsortbucket3outof4.txt
apache-hive-2.3.9-bin/examples/files/bool_literal.txt
apache-hive-2.3.9-bin/examples/files/nulls.txt
apache-hive-2.3.9-bin/examples/files/trunc_number1.txt
apache-hive-2.3.9-bin/examples/files/union_non_nullable.txt
apache-hive-2.3.9-bin/examples/files/alltypes.txt
apache-hive-2.3.9-bin/examples/files/srcbucket22.txt
apache-hive-2.3.9-bin/examples/files/srcbucket23.txt
apache-hive-2.3.9-bin/examples/files/SortCol1Col2.txt
apache-hive-2.3.9-bin/examples/files/binary.txt
apache-hive-2.3.9-bin/examples/files/customer_demographics.txt
apache-hive-2.3.9-bin/examples/files/2000_cols_data.csv
apache-hive-2.3.9-bin/examples/files/things.txt
apache-hive-2.3.9-bin/examples/files/kv3.txt
apache-hive-2.3.9-bin/examples/files/in7.txt
apache-hive-2.3.9-bin/examples/files/union_nullable.txt
apache-hive-2.3.9-bin/examples/files/string.txt
apache-hive-2.3.9-bin/examples/files/location.txt
apache-hive-2.3.9-bin/examples/queries/udf1.q
apache-hive-2.3.9-bin/examples/queries/input_testxpath.q
apache-hive-2.3.9-bin/examples/queries/join7.q
apache-hive-2.3.9-bin/examples/queries/input2.q
apache-hive-2.3.9-bin/examples/queries/groupby1.q
apache-hive-2.3.9-bin/examples/queries/sample6.q
apache-hive-2.3.9-bin/examples/queries/udf_case.q
apache-hive-2.3.9-bin/examples/queries/input8.q
apache-hive-2.3.9-bin/examples/queries/sample2.q
apache-hive-2.3.9-bin/examples/queries/input_testxpath2.q
apache-hive-2.3.9-bin/examples/queries/input6.q
apache-hive-2.3.9-bin/examples/queries/groupby5.q
apache-hive-2.3.9-bin/examples/queries/input_part1.q
apache-hive-2.3.9-bin/examples/queries/join3.q
apache-hive-2.3.9-bin/examples/queries/udf_when.q
apache-hive-2.3.9-bin/examples/queries/udf6.q
apache-hive-2.3.9-bin/examples/queries/union.q
apache-hive-2.3.9-bin/examples/queries/groupby2.q
apache-hive-2.3.9-bin/examples/queries/input1.q
apache-hive-2.3.9-bin/examples/queries/sample5.q
apache-hive-2.3.9-bin/examples/queries/join4.q
apache-hive-2.3.9-bin/examples/queries/sample1.q
apache-hive-2.3.9-bin/examples/queries/groupby6.q
apache-hive-2.3.9-bin/examples/queries/input5.q
apache-hive-2.3.9-bin/examples/queries/input_testsequencefile.q
apache-hive-2.3.9-bin/examples/queries/sample4.q
apache-hive-2.3.9-bin/examples/queries/groupby3.q
apache-hive-2.3.9-bin/examples/queries/join5.q
apache-hive-2.3.9-bin/examples/queries/case_sensitivity.q
apache-hive-2.3.9-bin/examples/queries/join1.q
apache-hive-2.3.9-bin/examples/queries/input4.q
apache-hive-2.3.9-bin/examples/queries/input20.q
apache-hive-2.3.9-bin/examples/queries/udf4.q
apache-hive-2.3.9-bin/examples/queries/cast1.q
apache-hive-2.3.9-bin/examples/queries/join6.q
apache-hive-2.3.9-bin/examples/queries/input9.q
apache-hive-2.3.9-bin/examples/queries/sample7.q
apache-hive-2.3.9-bin/examples/queries/input3.q
apache-hive-2.3.9-bin/examples/queries/input7.q
apache-hive-2.3.9-bin/examples/queries/groupby4.q
apache-hive-2.3.9-bin/examples/queries/sample3.q
apache-hive-2.3.9-bin/examples/queries/subq.q
apache-hive-2.3.9-bin/examples/queries/join2.q
apache-hive-2.3.9-bin/examples/queries/join8.q
apache-hive-2.3.9-bin/bin/ext/util/
apache-hive-2.3.9-bin/bin/hiveserver2
apache-hive-2.3.9-bin/bin/ext/cli.sh
apache-hive-2.3.9-bin/bin/ext/rcfilecat.sh
apache-hive-2.3.9-bin/bin/ext/orcfiledump.sh
apache-hive-2.3.9-bin/bin/ext/debug.sh
apache-hive-2.3.9-bin/bin/ext/util/execHiveCmd.sh
apache-hive-2.3.9-bin/bin/ext/llap.sh
apache-hive-2.3.9-bin/bin/ext/cleardanglingscratchdir.sh
apache-hive-2.3.9-bin/bin/ext/hbaseimport.sh
apache-hive-2.3.9-bin/bin/ext/lineage.sh
apache-hive-2.3.9-bin/bin/ext/llapstatus.sh
apache-hive-2.3.9-bin/bin/ext/help.sh
apache-hive-2.3.9-bin/bin/ext/schemaTool.sh
apache-hive-2.3.9-bin/bin/ext/metastore.sh
apache-hive-2.3.9-bin/bin/ext/beeline.sh
apache-hive-2.3.9-bin/bin/ext/hplsql.sh
apache-hive-2.3.9-bin/bin/ext/hiveserver2.sh
apache-hive-2.3.9-bin/bin/ext/metatool.sh
apache-hive-2.3.9-bin/bin/ext/jar.sh
apache-hive-2.3.9-bin/bin/ext/hiveburninclient.sh
apache-hive-2.3.9-bin/bin/ext/version.sh
apache-hive-2.3.9-bin/bin/ext/hbaseschematool.sh
apache-hive-2.3.9-bin/bin/ext/llapdump.sh
apache-hive-2.3.9-bin/bin/schematool
apache-hive-2.3.9-bin/bin/hive-config.sh
apache-hive-2.3.9-bin/bin/hplsql
apache-hive-2.3.9-bin/bin/hive
apache-hive-2.3.9-bin/bin/beeline
apache-hive-2.3.9-bin/bin/metatool
apache-hive-2.3.9-bin/scripts/llap/bin/runLlapDaemon.sh
apache-hive-2.3.9-bin/scripts/llap/bin/llapDaemon.sh
apache-hive-2.3.9-bin/scripts/llap/bin/llap-daemon-env.sh
apache-hive-2.3.9-bin/scripts/llap/sql/serviceCheckScript.sql
apache-hive-2.3.9-bin/scripts/llap/slider/package.py
apache-hive-2.3.9-bin/scripts/llap/slider/params.py
apache-hive-2.3.9-bin/scripts/llap/slider/templates.py
apache-hive-2.3.9-bin/scripts/llap/slider/llap.py
apache-hive-2.3.9-bin/scripts/llap/slider/argparse.py
apache-hive-2.3.9-bin/scripts/metastore/upgrade/oracle/
apache-hive-2.3.9-bin/scripts/metastore/upgrade/derby/
apache-hive-2.3.9-bin/scripts/metastore/upgrade/postgres/
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mysql/
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mssql/
apache-hive-2.3.9-bin/scripts/metastore/upgrade/oracle/037-HIVE-14496.oracle.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/oracle/upgrade.order.oracle
apache-hive-2.3.9-bin/scripts/metastore/upgrade/oracle/hive-schema-1.2.0.oracle.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/oracle/027-HIVE-12819.oracle.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/oracle/038-HIVE-10562.oracle.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/oracle/023-HIVE-12807.oracle.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/oracle/022-HIVE-11970.oracle.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/oracle/upgrade-2.0.0-to-2.1.0.oracle.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/oracle/hive-schema-0.13.0.oracle.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/oracle/hive-schema-0.14.0.oracle.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/oracle/013-HIVE-3255.oracle.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/oracle/hive-schema-1.3.0.oracle.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/oracle/018-HIVE-6757.oracle.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/oracle/hive-txn-schema-1.3.0.oracle.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/oracle/hive-schema-0.12.0.oracle.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/oracle/039-HIVE-12274.oracle.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/oracle/026-HIVE-12818.oracle.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/oracle/hive-schema-2.2.0.oracle.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/oracle/upgrade-2.1.0-to-2.2.0.oracle.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/oracle/011-HIVE-3649.oracle.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/oracle/hive-txn-schema-2.2.0.oracle.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/oracle/upgrade-0.10.0-to-0.11.0.oracle.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/oracle/hive-schema-0.9.0.oracle.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/oracle/upgrade-0.10.0-to-0.11.0.mysql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/oracle/031-HIVE-12381.oracle.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/oracle/019-HIVE-7118.oracle.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/oracle/hive-schema-2.3.0.oracle.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/oracle/upgrade-1.2.0-to-2.0.0.oracle.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/oracle/025-HIVE-12816.oracle.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/oracle/upgrade-1.1.0-to-1.2.0.oracle.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/oracle/hive-txn-schema-2.3.0.oracle.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/oracle/040-HIVE-16399.oracle.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/oracle/021-HIVE-9296.oracle.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/oracle/012-HIVE-1362.oracle.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/oracle/pre-0-upgrade-0.13.0-to-0.14.0.oracle.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/oracle/010-HIVE-3072.oracle.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/oracle/upgrade-0.13.0-to-0.14.0.oracle.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/oracle/030-HIVE-12823.oracle.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/oracle/hive-schema-2.1.0.oracle.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/oracle/016-HIVE-6386.oracle.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/oracle/hive-txn-schema-2.1.0.oracle.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/oracle/upgrade-0.14.0-to-1.1.0.oracle.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/oracle/024-HIVE-12814.oracle.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/oracle/017-HIVE-6458.oracle.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/oracle/hive-schema-2.0.0.oracle.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/oracle/034-HIVE-13076.oracle.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/oracle/028-HIVE-12821.oracle.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/oracle/hive-txn-schema-2.0.0.oracle.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/oracle/020-HIVE-7784.oracle.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/oracle/014-HIVE-3764.oracle.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/oracle/032-HIVE-12832.oracle.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/oracle/hive-schema-1.1.0.oracle.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/oracle/036-HIVE-13354.oracle.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/oracle/upgrade-0.9.0-to-0.10.0.oracle.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/oracle/hive-schema-0.10.0.oracle.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/oracle/029-HIVE-12822.oracle.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/oracle/upgrade-2.2.0-to-2.3.0.oracle.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/oracle/hive-txn-schema-0.14.0.oracle.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/oracle/upgrade-0.11.0-to-0.12.0.oracle.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/oracle/hive-txn-schema-0.13.0.oracle.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/oracle/upgrade-1.2.0-to-1.3.0.oracle.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/oracle/upgrade-0.12.0-to-0.13.0.oracle.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/oracle/hive-schema-0.11.0.oracle.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/oracle/035-HIVE-13395.oracle.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/derby/hive-schema-2.3.0.derby.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/derby/hive-txn-schema-2.3.0.derby.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/derby/upgrade-0.10.0-to-0.11.0.derby.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/derby/hive-schema-0.8.0.derby.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/derby/upgrade-2.0.0-to-2.1.0.derby.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/derby/hive-schema-0.11.0.derby.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/derby/012-HIVE-1362.derby.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/derby/upgrade-0.14.0-to-1.1.0.derby.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/derby/019-HIVE-7784.derby.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/derby/upgrade-2.1.0-to-2.2.0.derby.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/derby/upgrade-1.1.0-to-1.2.0.derby.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/derby/020-HIVE-9296.derby.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/derby/upgrade-0.6.0-to-0.7.0.derby.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/derby/upgrade-0.7.0-to-0.8.0.derby.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/derby/039-HIVE-12274.derby.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/derby/upgrade-1.2.0-to-2.0.0.derby.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/derby/hive-schema-0.4.0.derby.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/derby/hive-txn-schema-0.14.0.derby.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/derby/hive-txn-schema-1.3.0.derby.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/derby/hive-schema-1.3.0.derby.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/derby/hive-schema-0.14.0.derby.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/derby/010-HIVE-3072.derby.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/derby/026-HIVE-12818.derby.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/derby/002-HIVE-1068.derby.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/derby/005-HIVE-417.derby.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/derby/001-HIVE-972.derby.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/derby/027-HIVE-12819.derby.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/derby/hive-schema-0.7.0.derby.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/derby/004-HIVE-1364.derby.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/derby/017-HIVE-6458.derby.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/derby/028-HIVE-12821.derby.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/derby/upgrade-2.2.0-to-2.3.0.derby.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/derby/016-HIVE-6386.derby.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/derby/upgrade-1.2.0-to-1.3.0.derby.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/derby/upgrade-0.5.0-to-0.6.0.derby.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/derby/025-HIVE-12816.derby.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/derby/009-HIVE-2215.derby.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/derby/upgrade.order.derby
apache-hive-2.3.9-bin/scripts/metastore/upgrade/derby/006-HIVE-1823.derby.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/derby/hive-schema-0.4.1.derby.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/derby/014-HIVE-3764.derby.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/derby/upgrade-0.9.0-to-0.10.0.derby.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/derby/008-REVERT-HIVE-2246.derby.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/derby/hive-schema-0.12.0.derby.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/derby/037-HIVE-14496.derby.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/derby/023-HIVE-12807.derby.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/derby/hive-schema-2.0.0.derby.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/derby/README
apache-hive-2.3.9-bin/scripts/metastore/upgrade/derby/hive-txn-schema-2.0.0.derby.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/derby/hive-schema-1.2.0.derby.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/derby/031-HIVE-12831.derby.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/derby/upgrade-0.13.0-to-0.14.0.derby.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/derby/upgrade-0.8.0-to-0.9.0.derby.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/derby/008-HIVE-2246.derby.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/derby/hive-schema-0.5.0.derby.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/derby/003-HIVE-675.derby.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/derby/011-HIVE-3649.derby.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/derby/hive-schema-0.9.0.derby.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/derby/hive-schema-0.10.0.derby.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/derby/040-HIVE-16399.derby.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/derby/024-HIVE-12814.derby.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/derby/hive-txn-schema-2.2.0.derby.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/derby/hive-schema-2.2.0.derby.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/derby/032-HIVE-12832.derby.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/derby/021-HIVE-11970.derby.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/derby/hive-txn-schema-2.1.0.derby.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/derby/hive-schema-2.1.0.derby.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/derby/013-HIVE-3255.derby.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/derby/hive-schema-0.13.0.derby.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/derby/029-HIVE-12822.derby.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/derby/007-HIVE-78.derby.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/derby/018-HIVE-6757.derby.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/derby/hive-schema-0.3.0.derby.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/derby/hive-txn-schema-0.13.0.derby.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/derby/035-HIVE-13395.derby.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/derby/038-HIVE-10562.derby.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/derby/upgrade-0.12.0-to-0.13.0.derby.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/derby/hive-schema-0.6.0.derby.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/derby/036-HIVE-13354.derby.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/derby/upgrade-0.11.0-to-0.12.0.derby.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/derby/030-HIVE-12823.derby.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/derby/hive-schema-1.1.0.derby.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/derby/022-HIVE-11107.derby.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/derby/034-HIVE-13076.derby.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/postgres/hive-txn-schema-2.1.0.postgres.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/postgres/021-HIVE-11970.postgres.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/postgres/009-HIVE-2215.postgres.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/postgres/hive-schema-2.2.0.postgres.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/postgres/hive-schema-0.12.0.postgres.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/postgres/031-HIVE-12832.postgres.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/postgres/020-HIVE-9296.postgres.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/postgres/upgrade-2.1.0-to-2.2.0.postgres.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/postgres/008-REVERT-HIVE-2246.postgres.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/postgres/hive-schema-0.13.0.postgres.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/postgres/hive-schema-2.3.0.postgres.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/postgres/hive-schema-0.14.0.postgres.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/postgres/upgrade-1.2.0-to-1.3.0.postgres.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/postgres/hive-txn-schema-2.0.0.postgres.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/postgres/010-HIVE-3072.postgres.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/postgres/hive-schema-2.1.0.postgres.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/postgres/023-HIVE-12814.postgres.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/postgres/019-HIVE-7784.postgres.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/postgres/upgrade-0.10.0-to-0.11.0.postgres.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/postgres/hive-txn-schema-2.2.0.postgres.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/postgres/upgrade-0.9.0-to-0.10.0.postgres.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/postgres/upgrade-2.2.0-to-2.3.0.postgres.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/postgres/upgrade-0.14.0-to-1.1.0.postgres.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/postgres/upgrade-0.7.0-to-0.8.0.postgres.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/postgres/008-HIVE-2246.postgres.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/postgres/hive-schema-0.11.0.postgres.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/postgres/017-HIVE-6458.postgres.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/postgres/012-HIVE-1362.postgres.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/postgres/030-HIVE-12831.postgres.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/postgres/hive-schema-0.10.0.postgres.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/postgres/upgrade-0.11.0-to-0.12.0.postgres.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/postgres/upgrade-0.8.0-to-0.9.0.postgres.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/postgres/033-HIVE-13076.postgres.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/postgres/upgrade-1.1.0-to-1.2.0.postgres.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/postgres/hive-txn-schema-2.3.0.postgres.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/postgres/011-HIVE-3649.postgres.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/postgres/hive-schema-2.0.0.postgres.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/postgres/028-HIVE-12822.postgres.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/postgres/README
apache-hive-2.3.9-bin/scripts/metastore/upgrade/postgres/hive-schema-0.5.0.postgres.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/postgres/hive-txn-schema-0.14.0.postgres.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/postgres/hive-schema-1.1.0.postgres.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/postgres/hive-schema-0.4.1.postgres.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/postgres/hive-txn-schema-0.13.0.postgres.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/postgres/006-HIVE-1823.postgres.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/postgres/004-HIVE-1364.postgres.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/postgres/upgrade-0.13.0-to-0.14.0.postgres.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/postgres/038-HIVE-12274.postgres.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/postgres/upgrade-1.2.0-to-2.0.0.postgres.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/postgres/hive-txn-schema-1.3.0.postgres.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/postgres/pre-0-upgrade-0.13.0-to-0.14.0.postgres.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/postgres/026-HIVE-12819.postgres.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/postgres/hive-schema-0.3.0.postgres.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/postgres/upgrade-0.5.0-to-0.6.0.postgres.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/postgres/037-HIVE-10562.postgres.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/postgres/014-HIVE-3764.postgres.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/postgres/027-HIVE-12821.postgres.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/postgres/hive-schema-0.4.0.postgres.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/postgres/039-HIVE-16399.postgres.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/postgres/001-HIVE-972.postgres.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/postgres/016-HIVE-6386.postgres.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/postgres/007-HIVE-78.postgres.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/postgres/005-HIVE-417.postgres.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/postgres/hive-schema-0.6.0.postgres.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/postgres/hive-schema-0.9.0.postgres.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/postgres/018-HIVE-6757.postgres.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/postgres/upgrade.order.postgres
apache-hive-2.3.9-bin/scripts/metastore/upgrade/postgres/022-HIVE-12807.postgres.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/postgres/034-HIVE-13395.postgres.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/postgres/pre-0-upgrade-0.12.0-to-0.13.0.postgres.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/postgres/hive-schema-1.2.0.postgres.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/postgres/013-HIVE-3255.postgres.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/postgres/035-HIVE-13354.postgres.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/postgres/upgrade-0.12.0-to-0.13.0.postgres.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/postgres/hive-schema-1.3.0.postgres.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/postgres/002-HIVE-1068.postgres.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/postgres/upgrade-2.0.0-to-2.1.0.postgres.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/postgres/hive-schema-0.8.0.postgres.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/postgres/029-HIVE-12823.postgres.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/postgres/025-HIVE-12818.postgres.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/postgres/036-HIVE-14496.postgres.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/postgres/hive-schema-0.7.0.postgres.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/postgres/003-HIVE-675.postgres.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/postgres/upgrade-0.6.0-to-0.7.0.postgres.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/postgres/024-HIVE-12816.postgres.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mysql/upgrade-2.2.0-to-2.3.0.mysql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mysql/upgrade-0.5.0-to-0.6.0.mysql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mysql/016-HIVE-6386.mysql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mysql/upgrade-1.2.0-to-1.3.0.mysql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mysql/009-HIVE-2215.mysql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mysql/025-HIVE-12816.mysql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mysql/006-HIVE-1823.mysql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mysql/hive-schema-0.4.1.mysql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mysql/014-HIVE-3764.mysql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mysql/upgrade-0.9.0-to-0.10.0.mysql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mysql/hive-schema-0.12.0.mysql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mysql/037-HIVE-14496.mysql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mysql/023-HIVE-12807.mysql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mysql/hive-schema-2.0.0.mysql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mysql/hive-txn-schema-2.0.0.mysql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mysql/026-HIVE-12818.mysql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mysql/022-HIVE-11970.mysql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mysql/002-HIVE-1068.mysql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mysql/005-HIVE-417.mysql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mysql/001-HIVE-972.mysql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mysql/hive-schema-0.7.0.mysql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mysql/027-HIVE-12819.mysql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mysql/004-HIVE-1364.mysql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mysql/017-HIVE-6458.mysql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mysql/028-HIVE-12821.mysql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mysql/019-HIVE-7784.mysql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mysql/upgrade-2.1.0-to-2.2.0.mysql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mysql/020-HIVE-9296.mysql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mysql/upgrade-0.6.0-to-0.7.0.mysql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mysql/upgrade-1.1.0-to-1.2.0.mysql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mysql/upgrade-0.7.0-to-0.8.0.mysql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mysql/039-HIVE-12274.mysql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mysql/upgrade-1.2.0-to-2.0.0.mysql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mysql/hive-schema-0.4.0.mysql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mysql/hive-txn-schema-0.14.0.mysql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mysql/hive-txn-schema-1.3.0.mysql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mysql/hive-schema-1.3.0.mysql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mysql/hive-schema-0.14.0.mysql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mysql/010-HIVE-3072.mysql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mysql/hive-schema-2.3.0.mysql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mysql/hive-txn-schema-2.3.0.mysql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mysql/upgrade-0.10.0-to-0.11.0.mysql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mysql/upgrade-2.0.0-to-2.1.0.mysql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mysql/hive-schema-0.8.0.mysql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mysql/hive-schema-0.11.0.mysql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mysql/012-HIVE-1362.mysql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mysql/README
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mysql/upgrade-0.14.0-to-1.1.0.mysql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mysql/035-HIVE-13395.mysql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mysql/038-HIVE-10562.mysql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mysql/upgrade-0.12.0-to-0.13.0.mysql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mysql/hive-schema-0.6.0.mysql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mysql/036-HIVE-13354.mysql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mysql/upgrade-0.11.0-to-0.12.0.mysql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mysql/030-HIVE-12823.mysql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mysql/hive-schema-1.1.0.mysql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mysql/034-HIVE-13076.mysql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mysql/hive-txn-schema-2.1.0.mysql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mysql/013-HIVE-3255.mysql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mysql/hive-schema-2.1.0.mysql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mysql/hive-schema-0.13.0.mysql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mysql/007-HIVE-78.mysql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mysql/029-HIVE-12822.mysql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mysql/upgrade.order.mysql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mysql/018-HIVE-6757.mysql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mysql/hive-schema-0.3.0.mysql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mysql/hive-txn-schema-0.13.0.mysql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mysql/021-HIVE-7018.mysql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mysql/003-HIVE-675.mysql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mysql/011-HIVE-3649.mysql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mysql/hive-schema-0.9.0.mysql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mysql/hive-schema-0.10.0.mysql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mysql/040-HIVE-16399.mysql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mysql/hive-txn-schema-2.2.0.mysql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mysql/024-HIVE-12814.mysql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mysql/hive-schema-2.2.0.mysql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mysql/032-HIVE-12832.mysql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mysql/hive-schema-1.2.0.mysql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mysql/031-HIVE-12831.mysql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mysql/upgrade-0.13.0-to-0.14.0.mysql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mysql/upgrade-0.8.0-to-0.9.0.mysql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mysql/008-HIVE-2246.mysql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mysql/hive-schema-0.5.0.mysql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mssql/hive-schema-0.14.0.mssql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mssql/pre-0-upgrade-0.13.0-to-0.14.0.mssql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mssql/002-HIVE-7784.mssql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mssql/023-HIVE-10562.mssql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mssql/hive-schema-1.3.0.mssql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mssql/hive-txn-schema-0.14.0.mssql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mssql/upgrade-1.2.0-to-2.0.0.mssql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mssql/upgrade-1.1.0-to-1.2.0.mssql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mssql/upgrade-2.1.0-to-2.2.0.mssql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mssql/009-HIVE-12814.mssql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mssql/008-HIVE-12807.mssql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mssql/upgrade-0.14.0-to-1.1.0.mssql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mssql/pre-1-upgrade-0.12.0-to-0.13.0.mssql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mssql/024-HIVE-12274.mssql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mssql/hive-schema-0.11.0.mssql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mssql/upgrade-2.0.0-to-2.1.0.mssql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mssql/016-HIVE-12831.mssql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mssql/hive-schema-2.3.0.mssql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mssql/020-HIVE-13395.mssql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mssql/hive-schema-2.0.0.mssql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mssql/pre-1-upgrade-0.13.0-to-0.14.0.mssql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mssql/hive-schema-0.12.0.mssql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mssql/019-HIVE-13076.mssql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mssql/upgrade-1.2.0-to-1.3.0.mssql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mssql/007-HIVE-11970.mssql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mssql/upgrade-2.2.0-to-2.3.0.mssql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mssql/011-HIVE-12818.mssql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mssql/pre-0-upgrade-0.12.0-to-0.13.0.mssql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mssql/003-HIVE-8239.mssql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mssql/006-HIVE-9456.mssql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mssql/README
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mssql/hive-schema-2.2.0.mssql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mssql/005-HIVE-9296.mssql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mssql/013-HIVE-12821.mssql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mssql/017-HIVE-12832.mssql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mssql/upgrade-0.13.0-to-0.14.0.mssql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mssql/001-HIVE-6862.mssql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mssql/hive-schema-1.2.0.mssql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mssql/hive-schema-1.1.0.mssql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mssql/022-HIVE-14496.mssql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mssql/upgrade-0.12.0-to-0.13.0.mssql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mssql/004-HIVE-8550.mssql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mssql/012-HIVE-12819.mssql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mssql/021-HIVE-13354.mssql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mssql/hive-txn-schema-0.13.0.mssql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mssql/015-HIVE-12823.mssql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mssql/010-HIVE-12816.mssql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mssql/upgrade.order.mssql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mssql/hive-schema-0.13.0.mssql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mssql/hive-schema-2.1.0.mssql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mssql/025-HIVE-16399.mssql.sql
apache-hive-2.3.9-bin/scripts/metastore/upgrade/mssql/014-HIVE-12822.mssql.sql
apache-hive-2.3.9-bin/conf/ivysettings.xml
apache-hive-2.3.9-bin/conf/hive-default.xml.template
apache-hive-2.3.9-bin/conf/hive-env.sh.template
apache-hive-2.3.9-bin/lib/php/transport/
apache-hive-2.3.9-bin/lib/php/ext/
apache-hive-2.3.9-bin/lib/php/ext/thrift_protocol/
apache-hive-2.3.9-bin/lib/php/ext/thrift_protocol/tags/
apache-hive-2.3.9-bin/lib/php/ext/thrift_protocol/tags/1.0.0/
apache-hive-2.3.9-bin/lib/php/protocol/
apache-hive-2.3.9-bin/lib/php/packages/
apache-hive-2.3.9-bin/lib/php/packages/fb303/
apache-hive-2.3.9-bin/lib/php/autoload.php
apache-hive-2.3.9-bin/lib/php/transport/TTransport.php
apache-hive-2.3.9-bin/lib/php/transport/THttpClient.php
apache-hive-2.3.9-bin/lib/php/transport/TSocketPool.php
apache-hive-2.3.9-bin/lib/php/transport/TNullTransport.php
apache-hive-2.3.9-bin/lib/php/transport/TMemoryBuffer.php
apache-hive-2.3.9-bin/lib/php/transport/TSocket.php
apache-hive-2.3.9-bin/lib/php/transport/TFramedTransport.php
apache-hive-2.3.9-bin/lib/php/transport/TPhpStream.php
apache-hive-2.3.9-bin/lib/php/transport/TBufferedTransport.php
apache-hive-2.3.9-bin/lib/php/ext/thrift_protocol/tags/1.0.0/config.m4
apache-hive-2.3.9-bin/lib/php/ext/thrift_protocol/tags/1.0.0/php_thrift_protocol.cpp
apache-hive-2.3.9-bin/lib/php/ext/thrift_protocol/tags/1.0.0/php_thrift_protocol.h
apache-hive-2.3.9-bin/lib/php/ext/thrift_protocol/config.m4
apache-hive-2.3.9-bin/lib/php/ext/thrift_protocol/php_thrift_protocol.cpp
apache-hive-2.3.9-bin/lib/php/ext/thrift_protocol/php_thrift_protocol.h
apache-hive-2.3.9-bin/lib/php/Thrift.php
apache-hive-2.3.9-bin/lib/php/protocol/TBinaryProtocol.php
apache-hive-2.3.9-bin/lib/php/protocol/TProtocol.php
apache-hive-2.3.9-bin/lib/php/packages/fb303/FacebookService.php
apache-hive-2.3.9-bin/lib/php/packages/fb303/fb303_types.php
apache-hive-2.3.9-bin/lib/php/packages/serde/org/
apache-hive-2.3.9-bin/lib/php/packages/serde/org/apache/
apache-hive-2.3.9-bin/lib/php/packages/serde/org/apache/hadoop/
apache-hive-2.3.9-bin/lib/php/packages/serde/org/apache/hadoop/hive/
apache-hive-2.3.9-bin/lib/php/packages/serde/org/apache/hadoop/hive/serde/
apache-hive-2.3.9-bin/lib/php/packages/serde/org/apache/hadoop/hive/serde/Types.php
apache-hive-2.3.9-bin/lib/php/packages/serde/Types.php
apache-hive-2.3.9-bin/lib/php/packages/hive_metastore/metastore/
apache-hive-2.3.9-bin/lib/php/packages/hive_metastore/metastore/ThriftHiveMetastore.php
apache-hive-2.3.9-bin/lib/php/packages/hive_metastore/metastore/Types.php
apache-hive-2.3.9-bin/lib/php/packages/queryplan/Types.php
apache-hive-2.3.9-bin/lib/py/thrift/
apache-hive-2.3.9-bin/lib/py/thrift/transport/
apache-hive-2.3.9-bin/lib/py/thrift/server/
apache-hive-2.3.9-bin/lib/py/thrift/protocol/
apache-hive-2.3.9-bin/lib/py/thrift/reflection/
apache-hive-2.3.9-bin/lib/py/thrift/reflection/limited/
apache-hive-2.3.9-bin/lib/py/fb303_scripts/
apache-hive-2.3.9-bin/lib/py/fb303/
apache-hive-2.3.9-bin/lib/py/thrift/transport/TTransport.py
apache-hive-2.3.9-bin/lib/py/thrift/transport/TTwisted.py
apache-hive-2.3.9-bin/lib/py/thrift/transport/__init__.py
apache-hive-2.3.9-bin/lib/py/thrift/transport/TSocket.py
apache-hive-2.3.9-bin/lib/py/thrift/transport/THttpClient.py
apache-hive-2.3.9-bin/lib/py/thrift/__init__.py
apache-hive-2.3.9-bin/lib/py/thrift/server/__init__.py
apache-hive-2.3.9-bin/lib/py/thrift/server/TServer.py
apache-hive-2.3.9-bin/lib/py/thrift/server/THttpServer.py
apache-hive-2.3.9-bin/lib/py/thrift/server/TNonblockingServer.py
apache-hive-2.3.9-bin/lib/py/thrift/protocol/__init__.py
apache-hive-2.3.9-bin/lib/py/thrift/protocol/fastbinary.c
apache-hive-2.3.9-bin/lib/py/thrift/protocol/TBinaryProtocol.py
apache-hive-2.3.9-bin/lib/py/thrift/protocol/TProtocol.py
apache-hive-2.3.9-bin/lib/py/thrift/Thrift.py
apache-hive-2.3.9-bin/lib/py/thrift/TSCons.py
apache-hive-2.3.9-bin/lib/py/thrift/reflection/__init__.py
apache-hive-2.3.9-bin/lib/py/thrift/reflection/limited/constants.py
apache-hive-2.3.9-bin/lib/py/thrift/reflection/limited/__init__.py
apache-hive-2.3.9-bin/lib/py/thrift/reflection/limited/ttypes.py
apache-hive-2.3.9-bin/lib/py/fb303_scripts/fb303_simple_mgmt.py
apache-hive-2.3.9-bin/lib/py/fb303_scripts/__init__.py
apache-hive-2.3.9-bin/lib/py/fb303/constants.py
apache-hive-2.3.9-bin/lib/py/fb303/__init__.py
apache-hive-2.3.9-bin/lib/py/fb303/FacebookService-remote
apache-hive-2.3.9-bin/lib/py/fb303/ttypes.py
apache-hive-2.3.9-bin/lib/py/fb303/FacebookService.py
apache-hive-2.3.9-bin/lib/py/fb303/FacebookBase.py
apache-hive-2.3.9-bin/lib/py/hive_serde/constants.py
apache-hive-2.3.9-bin/lib/py/hive_serde/__init__.py
apache-hive-2.3.9-bin/lib/py/hive_serde/ttypes.py
apache-hive-2.3.9-bin/lib/py/hive_metastore/ThriftHiveMetastore-remote
apache-hive-2.3.9-bin/lib/py/hive_metastore/constants.py
apache-hive-2.3.9-bin/lib/py/hive_metastore/__init__.py
apache-hive-2.3.9-bin/lib/py/hive_metastore/ttypes.py
apache-hive-2.3.9-bin/lib/py/hive_metastore/ThriftHiveMetastore.py
apache-hive-2.3.9-bin/lib/py/queryplan/constants.py
apache-hive-2.3.9-bin/lib/py/queryplan/__init__.py
apache-hive-2.3.9-bin/lib/py/queryplan/ttypes.py
apache-hive-2.3.9-bin/hcatalog/bin/common.sh
apache-hive-2.3.9-bin/hcatalog/bin/hcatcfg.py
apache-hive-2.3.9-bin/hcatalog/bin/hcat.py
apache-hive-2.3.9-bin/hcatalog/bin/hcat
apache-hive-2.3.9-bin/hcatalog/etc/hcatalog/proto-hive-site.xml
apache-hive-2.3.9-bin/hcatalog/etc/hcatalog/jndi.properties
apache-hive-2.3.9-bin/hcatalog/etc/webhcat/webhcat-default.xml
apache-hive-2.3.9-bin/hcatalog/etc/webhcat/webhcat-log4j2.properties
apache-hive-2.3.9-bin/hcatalog/libexec/hcat-config.sh
apache-hive-2.3.9-bin/hcatalog/sbin/hcatcfg.py
apache-hive-2.3.9-bin/hcatalog/sbin/hcat_server.sh
apache-hive-2.3.9-bin/hcatalog/sbin/hcat_server.py
apache-hive-2.3.9-bin/hcatalog/sbin/update-hcatalog-env.sh
apache-hive-2.3.9-bin/hcatalog/sbin/webhcat_config.sh
apache-hive-2.3.9-bin/hcatalog/sbin/webhcat_server.sh
</code></pre>
<p>Create a symbolic soft link to unpacked hive binary package folder to /apps/hive folder which helps in upgrading the hive without any change to the configured environment variables.</p>
<pre><code>[hdpusr@masternode apps]$ pwd
/apps
[hdpusr@masternode apps]$ ls -ltrah
total 0
lrwxrwxrwx.  1 hdpusr hadoop  18 Dec  2  2020 hadoop -&gt; /apps/hadoop-2.7.7
drwxr-xr-x. 10 hdpusr hadoop 161 Dec  4  2020 hadoop-2.7.7
lrwxrwxrwx.  1 hdpusr hadoop  31 Dec  7  2020 spark -&gt; /apps/spark-2.4.7-bin-hadoop2.7
dr-xr-xr-x. 18 root   root   253 Dec  8  2020 ..
drwxr-xr-x. 15 hdpusr hadoop 235 Dec  8  2020 spark-2.4.7-bin-hadoop2.7
drwxr-xr-x.  4 hdpusr hadoop 169 Dec  8  2020 hadoopdata
drwxr-xr-x.  3 hdpusr hadoop  55 Dec 12  2020 scalaLearning
drwxr-xr-x.  7 hdpusr hadoop 256 Jun  3 10:07 pysparkExercises
drwxrwxrwx.  2 root   root   165 Jul 28 18:38 downloads
drwxr-xr-x.  9 hdpusr hadoop 195 Jul 28 18:50 .
drwxr-xr-x. 10 hdpusr hadoop 184 Jul 28 18:50 apache-hive-2.3.9-bin
[hdpusr@masternode apps]$ ln -sf apache-hive-2.3.9-bin hive
[hdpusr@masternode apps]$ ls -ltrah
total 0
lrwxrwxrwx.  1 hdpusr hadoop  18 Dec  2  2020 hadoop -&gt; /apps/hadoop-2.7.7
drwxr-xr-x. 10 hdpusr hadoop 161 Dec  4  2020 hadoop-2.7.7
lrwxrwxrwx.  1 hdpusr hadoop  31 Dec  7  2020 spark -&gt; /apps/spark-2.4.7-bin-hadoop2.7
dr-xr-xr-x. 18 root   root   253 Dec  8  2020 ..
drwxr-xr-x. 15 hdpusr hadoop 235 Dec  8  2020 spark-2.4.7-bin-hadoop2.7
drwxr-xr-x.  4 hdpusr hadoop 169 Dec  8  2020 hadoopdata
drwxr-xr-x.  3 hdpusr hadoop  55 Dec 12  2020 scalaLearning
drwxr-xr-x.  7 hdpusr hadoop 256 Jun  3 10:07 pysparkExercises
drwxrwxrwx.  2 root   root   165 Jul 28 18:38 downloads
drwxr-xr-x. 10 hdpusr hadoop 184 Jul 28 18:50 apache-hive-2.3.9-bin
lrwxrwxrwx.  1 hdpusr hadoop  21 Jul 28 18:59 hive -&gt; apache-hive-2.3.9-bin
drwxr-xr-x.  9 hdpusr hadoop 207 Jul 28 18:59 .
[hdpusr@masternode apps]$ cd hive
[hdpusr@masternode hive]$ ls -ltrah
total 56K
-rw-r--r--.  1 hdpusr hadoop  230 Jun  2 02:13 NOTICE
-rw-r--r--.  1 hdpusr hadoop  21K Jun  2 02:13 LICENSE
-rw-r--r--.  1 hdpusr hadoop  667 Jun  2 02:22 RELEASE_NOTES.txt
drwxr-xr-x.  2 hdpusr hadoop   44 Jul 28 18:50 jdbc
drwxr-xr-x.  2 hdpusr hadoop 4.0K Jul 28 18:50 binary-package-licenses
drwxr-xr-x.  4 hdpusr hadoop   34 Jul 28 18:50 examples
drwxr-xr-x.  3 hdpusr hadoop  133 Jul 28 18:50 bin
drwxr-xr-x. 10 hdpusr hadoop  184 Jul 28 18:50 .
drwxr-xr-x.  4 hdpusr hadoop   35 Jul 28 18:50 scripts
drwxr-xr-x.  2 hdpusr hadoop 4.0K Jul 28 18:50 conf
drwxr-xr-x.  4 hdpusr hadoop  12K Jul 28 18:50 lib
drwxr-xr-x.  7 hdpusr hadoop   68 Jul 28 18:50 hcatalog
drwxr-xr-x.  9 hdpusr hadoop  207 Jul 28 18:59 ..
</code></pre>
<h2 id="setup-hive-environment-variables">Setup Hive Environment Variables</h2>
<p>Along with the Hadoop environment variables, set the Hive env variables in the .bashrc file of the user who owns the Hadoop installation ie., hdpusr in my cluster.</p>
<pre><code>#.bashrc    
#Source global definitions
if [ -f /etc/bashrc ]; then
        . /etc/bashrc
fi    
#User specific environment
PATH="$HOME/.local/bin:$HOME/bin:$PATH"
export PATH    
#==================================================    
#HADOOP VARIABLES
export HADOOP_HOME=/apps/hadoop
export JAVA_HOME=/usr/lib/jvm/jdk
export HADOOP_INSTALL=/apps/hadoop
export HADOOP_MAPRED_HOME=$HADOOP_INSTALL
export HADOOP_COMMON_HOME=$HADOOP_INSTALL
export HADOOP_HDFS_HOME=$HADOOP_INSTALL
export YARN_HOME=$HADOOP_INSTALL
export HADOOP_COMMON_LIB_NATIVE_DIR=$HADOOP_INSTALL/lib/native
export HADOOP_OPTS="-Djava.library.path=$HADOOP_INSTALL/lib/native"
export HADOOP_CONF_DIR=$HADOOP_INSTALL/etc/hadoop
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:$HADOOP_INSTALL/lib/native
export PATH=$PATH:$JAVA_HOME/bin:$HADOOP_INSTALL/bin:$HADOOP_INSTALL/sbin
#==================================================    
#SPARK VARIABLES
export YARN_CONF_DIR=$HADOOP_INSTALL/etc/hadoop
export SPARK_HOME=/apps/spark
export PATH=$PATH:$SPARK_HOME/bin
export PYSPARK_PYTHON=/usr/bin/python3
export PATH=$PATH:/usr/bin:$SPARK_HOME/bin:$SPARK_HOME/sbin
#==================================================    
#HIVE VARIABLES
export HIVE_HOME=/apps/hive    
export PATH=$PATH:$HIVE_HOME/bin
#==================================================
</code></pre>
<h2 id="setup-hive-hdfs-folders-in-hadoop-filesystem">Setup Hive HDFS Folders in Hadoop FileSystem</h2>
<p>Start hadoop services using <a href="http://start-dfs.sh">start-dfs.sh</a> as below and check the services using jps (Java Virtual Machine Process Status) tool.</p>
<pre><code>[hdpusr@masternode ~]$ start-dfs.sh
Starting namenodes on [masternode]
masternode: starting namenode, logging to /apps/hadoop-2.7.7/logs/hadoop-hdpusr-namenode-masternode.out
192.168.1.201: starting datanode, logging to /apps/hadoop-2.7.7/logs/hadoop-hdpusr-datanode-executornode1.out
192.168.1.200: starting datanode, logging to /apps/hadoop-2.7.7/logs/hadoop-hdpusr-datanode-masternode.out
Starting secondary namenodes [0.0.0.0]
0.0.0.0: starting secondarynamenode, logging to /apps/hadoop-2.7.7/logs/hadoop-hdpusr-secondarynamenode-masternode.out
[hdpusr@masternode ~]$ jps
55856 NameNode
56017 DataNode
56387 SecondaryNameNode
59934 Jps
</code></pre>
<p>Hive uses Hadoop hdfs to store the tmp files and metastore related files in warehouse directory. So we need to create the below folders in hdfs for Hive to work properly and leverage them.</p>
<pre><code>[hdpusr@masternode ~]$ hdfs dfs -mkdir /tmp
[hdpusr@masternode ~]$ hdfs dfs -mkdir -p /user/hive/warehouse
[hdpusr@masternode ~]$ hdfs dfs -chmod g+w /tmp
[hdpusr@masternode ~]$ hdfs dfs -chmod g+w /user/hive/warehouse
</code></pre>
<h2 id="install-mysql-server">Install MySQL Server</h2>
<p>To install MySQL server run the below yum install command using root user</p>
<pre><code>[root@masternode ~]# yum install mysql-server
Last metadata expiration check: 1:38:08 ago on Thu 29 Jul 2021 12:34:34 AM IST.
Dependencies resolved.
=============================================================================================================================================================
 Package                                     Architecture            Version                                                Repository                  Size
=============================================================================================================================================================
Installing:
 mysql-server                                x86_64                  8.0.21-1.module_el8.2.0+493+63b41e36                   AppStream                   22 M
Installing dependencies:
 mariadb-connector-c-config                  noarch                  3.1.11-2.el8_3                                         AppStream                   15 k
 mecab                                       x86_64                  0.996-1.module_el8.2.0+493+63b41e36.9                  AppStream                  393 k
 mysql                                       x86_64                  8.0.21-1.module_el8.2.0+493+63b41e36                   AppStream                   12 M
 mysql-common                                x86_64                  8.0.21-1.module_el8.2.0+493+63b41e36                   AppStream                  148 k
 mysql-errmsg                                x86_64                  8.0.21-1.module_el8.2.0+493+63b41e36                   AppStream                  581 k
 protobuf-lite                               x86_64                  3.5.0-13.el8                                           AppStream                  149 k
Enabling module streams:
 mysql                                                               8.0
Transaction Summary
=============================================================================================================================================================
Install  7 Packages
Total download size: 35 M
Installed size: 182 M
Is this ok [y/N]: y
Downloading Packages:
(1/7): mariadb-connector-c-config-3.1.11-2.el8_3.noarch.rpm                                                                   90 kB/s |  15 kB     00:00
(2/7): mysql-common-8.0.21-1.module_el8.2.0+493+63b41e36.x86_64.rpm                                                          1.0 MB/s | 148 kB     00:00
(3/7): mysql-errmsg-8.0.21-1.module_el8.2.0+493+63b41e36.x86_64.rpm                                                          1.6 MB/s | 581 kB     00:00
(4/7): mecab-0.996-1.module_el8.2.0+493+63b41e36.9.x86_64.rpm                                                                592 kB/s | 393 kB     00:00
(5/7): protobuf-lite-3.5.0-13.el8.x86_64.rpm                                                                                 547 kB/s | 149 kB     00:00
(6/7): mysql-8.0.21-1.module_el8.2.0+493+63b41e36.x86_64.rpm                                                                 1.0 MB/s |  12 MB     00:11
(7/7): mysql-server-8.0.21-1.module_el8.2.0+493+63b41e36.x86_64.rpm                                                          1.7 MB/s |  22 MB     00:13
-------------------------------------------------------------------------------------------------------------------------------------------------------------
Total                                                                                                                        2.4 MB/s |  35 MB     00:14
Running transaction check
Transaction check succeeded.
Running transaction test
Transaction test succeeded.
Running transaction
  Preparing        :                                                                                                                                     1/1
  Installing       : mariadb-connector-c-config-3.1.11-2.el8_3.noarch                                                                                    1/7
  Installing       : mysql-common-8.0.21-1.module_el8.2.0+493+63b41e36.x86_64                                                                            2/7
  Installing       : mysql-8.0.21-1.module_el8.2.0+493+63b41e36.x86_64                                                                                   3/7
  Installing       : mysql-errmsg-8.0.21-1.module_el8.2.0+493+63b41e36.x86_64                                                                            4/7
  Installing       : protobuf-lite-3.5.0-13.el8.x86_64                                                                                                   5/7
  Installing       : mecab-0.996-1.module_el8.2.0+493+63b41e36.9.x86_64                                                                                  6/7
  Running scriptlet: mecab-0.996-1.module_el8.2.0+493+63b41e36.9.x86_64                                                                                  6/7
  Running scriptlet: mysql-server-8.0.21-1.module_el8.2.0+493+63b41e36.x86_64                                                                            7/7
  Installing       : mysql-server-8.0.21-1.module_el8.2.0+493+63b41e36.x86_64                                                                            7/7
  Running scriptlet: mysql-server-8.0.21-1.module_el8.2.0+493+63b41e36.x86_64                                                                            7/7
ValueError: File context for /var/log/mysql(/.*)? already defined
  Verifying        : mariadb-connector-c-config-3.1.11-2.el8_3.noarch                                                                                    1/7
  Verifying        : mecab-0.996-1.module_el8.2.0+493+63b41e36.9.x86_64                                                                                  2/7
  Verifying        : mysql-8.0.21-1.module_el8.2.0+493+63b41e36.x86_64                                                                                   3/7
  Verifying        : mysql-common-8.0.21-1.module_el8.2.0+493+63b41e36.x86_64                                                                            4/7
  Verifying        : mysql-errmsg-8.0.21-1.module_el8.2.0+493+63b41e36.x86_64                                                                            5/7
  Verifying        : mysql-server-8.0.21-1.module_el8.2.0+493+63b41e36.x86_64                                                                            6/7
  Verifying        : protobuf-lite-3.5.0-13.el8.x86_64                                                                                                   7/7
Installed products updated.
Installed:
  mariadb-connector-c-config-3.1.11-2.el8_3.noarch                              mecab-0.996-1.module_el8.2.0+493+63b41e36.9.x86_64
  mysql-8.0.21-1.module_el8.2.0+493+63b41e36.x86_64                             mysql-common-8.0.21-1.module_el8.2.0+493+63b41e36.x86_64
  mysql-errmsg-8.0.21-1.module_el8.2.0+493+63b41e36.x86_64                      mysql-server-8.0.21-1.module_el8.2.0+493+63b41e36.x86_64
  protobuf-lite-3.5.0-13.el8.x86_64
Complete!
</code></pre>
<p>Start MySQL deamon and enable it to run on machine boot automatically using systemctl utility.</p>
<pre><code>[root@masternode ~]# ps -ef|grep mysql
root      114710   81660  0 02:24 pts/0    00:00:00 grep --color=auto mysql
[root@masternode ~]# systemctl start mysqld
[root@masternode ~]# ps -ef|grep mysql
mysql     115711       1 14 02:26 ?        00:00:01 /usr/libexec/mysqld --basedir=/usr
root      115836   81660  0 02:26 pts/0    00:00:00 grep --color=auto mysql
[root@masternode ~]# systemctl enable mysqld
Created symlink /etc/systemd/system/multi-user.target.wants/mysqld.service → /usr/lib/systemd/system/mysqld.service.
[root@masternode ~]# systemctl status mysqld
● mysqld.service - MySQL 8.0 database server
   Loaded: loaded (/usr/lib/systemd/system/mysqld.service; enabled; vendor preset: disabled)
   Active: active (running) since Thu 2021-07-29 02:26:27 IST; 3min 13s ago
 Main PID: 115711 (mysqld)
   Status: "Server is operational"
    Tasks: 38 (limit: 27385)
   Memory: 336.2M
   CGroup: /system.slice/mysqld.service
           └─115711 /usr/libexec/mysqld --basedir=/usr
Jul 29 02:26:26 masternode systemd[1]: Starting MySQL 8.0 database server...
Jul 29 02:26:27 masternode systemd[1]: Started MySQL 8.0 database server.
</code></pre>
<p>Create Hive User and Hive metastore database in MySQL which will be used to configure MySQL DB as metastore for Hive.</p>
<pre><code>[root@masternode ~]# mysql -u root
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 10
Server version: 8.0.21 Source distribution
Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.
Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.
Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
mysql&gt;
mysql&gt; CREATE USER 'hive'@'localhost' IDENTIFIED BY 'hive' password expire never;
Query OK, 0 rows affected (0.03 sec)
mysql&gt; GRANT ALL ON *.* TO 'hive'@'localhost';
Query OK, 0 rows affected (0.05 sec)
mysql&gt; exit
Bye
[root@masternode ~]# mysql -u hive -phive
mysql: [Warning] Using a password on the command line interface can be insecure.
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 11
Server version: 8.0.21 Source distribution
Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.
Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.
Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
mysql&gt; create database hive_metastore;
Query OK, 1 row affected (0.02 sec)
mysql&gt; show databases;
+--------------------+
| Database           |
+--------------------+
| hive_metastore     |
| information_schema |
| mysql              |
| performance_schema |
| sys                |
+--------------------+
5 rows in set (0.00 sec)
</code></pre>
<h2 id="configure-hive-metastore">Configure Hive Metastore</h2>
<p>To configure the Hive remote metastore in MySQL, we need the mysql jdbc connector jar file. The compatiable version of jdbc connector can be downloaded from Maven central repo that works with the MySQL DB version installed.<br>
Find the MySQL DB version by running the below command.</p>
<pre><code>[root@masternode ~]# mysql -V
mysql  Ver 8.0.21 for Linux on x86_64 (Source distribution)
</code></pre>
<p>The compatible jdbc driver connector can be downloaded from the below url and move the same into Hive lib directory<br>
<a href="https://repo1.maven.org/maven2/mysql/mysql-connector-java/8.0.21/mysql-connector-java-8.0.21.jar">https://repo1.maven.org/maven2/mysql/mysql-connector-java/8.0.21/mysql-connector-java-8.0.21.jar</a></p>
<pre><code>[hdpusr@masternode downloads]$ wget https://repo1.maven.org/maven2/mysql/mysql-connector-java/8.0.21/mysql-connector-java-8.0.21.jar
--2021-07-29 04:11:09--  https://repo1.maven.org/maven2/mysql/mysql-connector-java/8.0.21/mysql-connector-java-8.0.21.jar
Resolving repo1.maven.org (repo1.maven.org)... 151.101.52.209
Connecting to repo1.maven.org (repo1.maven.org)|151.101.52.209|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 2397321 (2.3M) [application/java-archive]
Saving to: ‘mysql-connector-java-8.0.21.jar’
mysql-connector-java-8.0.21.jar         100%[============================================================================&gt;]   2.29M   162KB/s    in 11s
2021-07-29 04:11:22 (210 KB/s) - ‘mysql-connector-java-8.0.21.jar’ saved [2397321/2397321]
[hdpusr@masternode downloads]$ ls -ltrah
total 968M
-rw-r--r--. 1 hdpusr  hadoop  2.3M Jun 16  2020 mysql-connector-java-8.0.21.jar
-rw-r--r--. 1 root    root    125M Jul 13  2020 scala-2.12.12.rpm
-rw-r--r--. 1 hdpusr  hadoop  223M Sep  8  2020 spark-2.4.7-bin-hadoop2.7.tgz
-rw-rw-r--. 1 rvalusa rvalusa 137M Dec  2  2020 jdk-8u271-linux-x64.tar.gz
-rw-r--r--. 1 hdpusr  hadoop  209M Dec  2  2020 hadoop-2.7.7.tar.gz
-rw-r--r--. 1 hdpusr  hadoop  273M Jun 10 00:00 apache-hive-2.3.9-bin.tar.gz
drwxr-xr-x. 9 hdpusr  hadoop   207 Jul 28 18:59 ..
drwxrwxrwx. 2 root    root     204 Jul 29 04:11 .
[hdpusr@masternode downloads]$ mv mysql-connector-java-8.0.21.jar $HIVE_HOME/lib
[hdpusr@masternode downloads]$ ls -ltrah $HIVE_HOME/lib/mysql-connector*
-rw-r--r--. 1 hdpusr hadoop 2.3M Jun 16  2020 /apps/hive/lib/mysql-connector-java-8.0.21.jar
</code></pre>
<h2 id="configure-hive-file-hive-site.xml">Configure Hive File hive-site.xml</h2>
<p>Create required configuration files as below</p>
<pre><code>[hdpusr@masternode conf]$ pwd
/apps/hive/conf
[hdpusr@masternode conf]$ ls -ltrah
total 292K
-rw-r--r--.  1 hdpusr hadoop 2.6K Jun  2 02:13 parquet-logging.properties
-rw-r--r--.  1 hdpusr hadoop 2.1K Jun  2 02:13 ivysettings.xml
-rw-r--r--.  1 hdpusr hadoop 2.9K Jun  2 02:13 hive-log4j2.properties.template
-rw-r--r--.  1 hdpusr hadoop 2.4K Jun  2 02:13 hive-env.sh.template
-rw-r--r--.  1 hdpusr hadoop 1.6K Jun  2 02:13 beeline-log4j2.properties.template
-rw-r--r--.  1 hdpusr hadoop 6.9K Jun  2 02:13 llap-daemon-log4j2.properties.template
-rw-r--r--.  1 hdpusr hadoop 2.7K Jun  2 02:13 llap-cli-log4j2.properties.template
-rw-r--r--.  1 hdpusr hadoop 2.3K Jun  2 02:13 hive-exec-log4j2.properties.template
-rw-r--r--.  1 hdpusr hadoop 252K Jun  2 02:24 hive-default.xml.template
drwxr-xr-x. 10 hdpusr hadoop  184 Jul 28 18:50 ..
drwxr-xr-x.  2 hdpusr hadoop 4.0K Jul 28 18:50 .
[hdpusr@masternode conf]$ cp hive-default.xml.template hive-site.xml
[hdpusr@masternode conf]$ cp hive-exec-log4j2.properties.template hive-execlog4j2.properties
[hdpusr@masternode conf]$ cp hive-log4j2.properties.template hive-log4j2.properties
[hdpusr@masternode conf]$ ls -ltrah
total 552K
-rw-r--r--.  1 hdpusr hadoop 2.6K Jun  2 02:13 parquet-logging.properties
-rw-r--r--.  1 hdpusr hadoop 2.1K Jun  2 02:13 ivysettings.xml
-rw-r--r--.  1 hdpusr hadoop 2.9K Jun  2 02:13 hive-log4j2.properties.template
-rw-r--r--.  1 hdpusr hadoop 2.4K Jun  2 02:13 hive-env.sh.template
-rw-r--r--.  1 hdpusr hadoop 1.6K Jun  2 02:13 beeline-log4j2.properties.template
-rw-r--r--.  1 hdpusr hadoop 6.9K Jun  2 02:13 llap-daemon-log4j2.properties.template
-rw-r--r--.  1 hdpusr hadoop 2.7K Jun  2 02:13 llap-cli-log4j2.properties.template
-rw-r--r--.  1 hdpusr hadoop 2.3K Jun  2 02:13 hive-exec-log4j2.properties.template
-rw-r--r--.  1 hdpusr hadoop 252K Jun  2 02:24 hive-default.xml.template
drwxr-xr-x. 10 hdpusr hadoop  184 Jul 28 18:50 ..
-rw-r--r--.  1 hdpusr hadoop 252K Jul 29 04:50 hive-site.xml
-rw-r--r--.  1 hdpusr hadoop 2.3K Jul 29 04:51 hive-execlog4j2.properties
-rw-r--r--.  1 hdpusr hadoop 2.9K Jul 29 04:52 hive-log4j2.properties
drwxr-xr-x.  2 hdpusr hadoop 4.0K Jul 29 04:52 .
</code></pre>
<p>Modify $HIVE_HOME/conf/hive-site.xml, which has some important parameters to set:</p>
<p>hive.metastore.warehourse.dir: This is the path to the Hive warehouse location. By default, it is at /user/hive/warehouse.<br>
hive.exec.scratchdir: This is the temporary data file location. By default, it is at /tmp/hive-${<a href="http://user.name">user.name</a>}’.</p>
<pre><code>  ...
  ...
  &lt;property&gt;
    &lt;name&gt;hive.metastore.warehouse.dir&lt;/name&gt;
    &lt;value&gt;/user/hive/warehouse&lt;/value&gt;
    &lt;description&gt;location of default database for the warehouse&lt;/description&gt;
  &lt;/property&gt;
  &lt;property&gt;
    &lt;name&gt;hive.exec.scratchdir&lt;/name&gt;
    &lt;value&gt;/tmp/hive&lt;/value&gt;
    &lt;description&gt;HDFS root scratch dir for Hive jobs which gets created with write all (733) permission. For each connecting user, an HDFS scratch dir: ${hive.exec.scratchdir}/&amp;lt;username&amp;gt; is created, with ${hive.scratch.dir.permission}.&lt;/description&gt;
  &lt;/property&gt;
  &lt;property&gt;
    &lt;name&gt;hive.scratch.dir.permission&lt;/name&gt;
    &lt;value&gt;700&lt;/value&gt;
    &lt;description&gt;The permission for the user specific scratch directories that get created.&lt;/description&gt;
  &lt;/property&gt;
  ...
  ...
</code></pre>
<p>To configure the metastore on remote databases, the following parameters should be configured in hive-site.xml:</p>
<ul>
<li>javax.jdo.option.ConnectionURL: This is the JDBC URL database - <strong>jdbc:mysql://localhost/hive_metastore</strong></li>
<li>javax.jdo.option.ConnectionDriverName: This is the JDBC driver class name - <strong>com.mysql.cj.jdbc.Driver</strong> -<br>
<a href="https://dev.mysql.com/doc/connector-j/8.0/en/connector-j-reference-driver-name.html">https://dev.mysql.com/doc/connector-j/8.0/en/connector-j-reference-driver-name.html</a></li>
<li>javax.jdo.option.ConnectionUserName: This is the username used to access the database - <strong>hive</strong></li>
<li>javax.jdo.option.ConnectionPassword: This    is the password used to access the database - <strong>hive</strong></li>
<li>hive.metastore.uris: <strong>thrift://127.0.0.1:9083</strong></li>
</ul>
<p>hive-site.xml configuration changes for the above parameters look like below</p>
<pre><code>  ...
  &lt;property&gt;
    &lt;name&gt;javax.jdo.option.ConnectionURL&lt;/name&gt;
    &lt;!-- &lt;value&gt;jdbc:derby:;databaseName=metastore_db;create=true&lt;/value&gt; --&gt;
    &lt;value&gt;jdbc:mysql://localhost/hive_metastore?createDatabaseIfNotExist=true&lt;/value&gt;
    &lt;description&gt;
      JDBC connect string for a JDBC metastore.
      To use SSL to encrypt/authenticate the connection, provide database-specific SSL flag in the connection URL.
      For example, jdbc:postgresql://myhost/db?ssl=true for postgres database.
    &lt;/description&gt;
  &lt;/property&gt;
  &lt;property&gt;
    &lt;name&gt;javax.jdo.option.ConnectionDriverName&lt;/name&gt;
    &lt;!--&lt;value&gt;org.apache.derby.jdbc.EmbeddedDriver&lt;/value&gt;--&gt;
    &lt;value&gt;com.mysql.cj.jdbc.Driver&lt;/value&gt;
    &lt;description&gt;Driver class name for a JDBC metastore&lt;/description&gt;
  &lt;/property&gt;
  &lt;property&gt;
    &lt;name&gt;javax.jdo.option.ConnectionUserName&lt;/name&gt;
    &lt;!--&lt;value&gt;APP&lt;/value&gt;--&gt;
    &lt;value&gt;hive&lt;/value&gt;
    &lt;description&gt;Username to use against metastore database&lt;/description&gt;
  &lt;/property&gt;
  &lt;property&gt;
    &lt;name&gt;javax.jdo.option.ConnectionPassword&lt;/name&gt;
    &lt;!--&lt;value&gt;mine&lt;/value&gt;--&gt;
    &lt;value&gt;hive&lt;/value&gt;
    &lt;description&gt;password to use against metastore database&lt;/description&gt;
  &lt;/property&gt;
  &lt;property&gt;
    &lt;name&gt;hive.metastore.uris&lt;/name&gt;
    &lt;value&gt;thrift://127.0.0.1:9083&lt;/value&gt;
    &lt;description&gt;Thrift URI for the remote metastore. Used by metastore client to connect to remote metastore.&lt;/description&gt;
  &lt;/property&gt;
  ...
  ...
</code></pre>


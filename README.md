#  Spark with Cook Support

In order to build this package, you need to build and install `cook jobclient` first.

```
# Check out cook jobclient and install to local m2 repository
git clone https://github.com/twosigma/Cook.git
cd Cook/jobclient
mvn package
mvn org.apache.maven.plugins:maven-install-plugin:2.5.2:install-file \
  -Dfile=target/cook-jobclient-0.2.0-SNAPSHOT.jar \
  -DpomFile=pom.xml
```

Now, we are ready to build the Spark distribution as follows. Note that if you are using Java 7, we
probably need to increase heap size used by Maven a little bit. However, if you are on Java 8, you
could ignore the following step.
```
export MAVEN_OPTS="-Xmx4g -XX:MaxPermSize=1024M -XX:ReservedCodeCacheSize=1024m"
```
Then, we could
```
./dev/make-distribution.sh --tgz --name hadoop-provided-scala2.11 -Dscala-2.11 -Phadoop-2.6,hadoop-provided,hive,cook -DskipTests
```

Note: you need to make sure to include "cook" among the optional profiles (-P).

The tarball will be created with the hadoop version and scala version
embedded in the tarball name.  Additionally, we use `git describe
--tags` to create the spark version, rather than just taking what's in
the pom.xml files.  Check that the Apache Spark tag for your version is
available, for example (for version 2.2.1):
```
git tag
...
v2.2.1
...
``` 

If your tag does not exist yet, you can either:
1. Fetch it from the upstream Apache Spark repository.  For example (for version 2.2.1):
```
git fetch https://github.com/apache/spark.git refs/tags/v2.2.1:refs/tags/v2.2.1
```
2. **Or**, manually tag the specific commit marking the version:
```
git tag -a v2.2.1 e30e2698a2 -m "Add tag for v2.2.1"
```

This way, we get a tarball name that looks like

    spark-2.2.1-20-g9dc4df0-bin-hadoop-provided-scala2.11.tgz

rather than

    spark-2.0.2-bin-hadoop-provided-scala2.11.tgz

and thus we can manage multiple internal releases on the same upstream
version, and also manage our scala version dependencies appropriately.

# Apache Spark

Spark is a fast and general cluster computing system for Big Data. It provides
high-level APIs in Scala, Java, Python, and R, and an optimized engine that
supports general computation graphs for data analysis. It also supports a
rich set of higher-level tools including Spark SQL for SQL and DataFrames,
MLlib for machine learning, GraphX for graph processing,
and Spark Streaming for stream processing.

<http://spark.apache.org/>


## Online Documentation

You can find the latest Spark documentation, including a programming
guide, on the [project web page](http://spark.apache.org/documentation.html).
This README file only contains basic setup instructions.

## Building Spark

Spark is built using [Apache Maven](http://maven.apache.org/).
To build Spark and its example programs, run:

    build/mvn -DskipTests clean package

(You do not need to do this if you downloaded a pre-built package.)

You can build Spark using more than one thread by using the -T option with Maven, see ["Parallel builds in Maven 3"](https://cwiki.apache.org/confluence/display/MAVEN/Parallel+builds+in+Maven+3).
More detailed documentation is available from the project site, at
["Building Spark"](http://spark.apache.org/docs/latest/building-spark.html).

For general development tips, including info on developing Spark using an IDE, see ["Useful Developer Tools"](http://spark.apache.org/developer-tools.html).

## Interactive Scala Shell

The easiest way to start using Spark is through the Scala shell:

    ./bin/spark-shell

Try the following command, which should return 1000:

    scala> sc.parallelize(1 to 1000).count()

## Interactive Python Shell

Alternatively, if you prefer Python, you can use the Python shell:

    ./bin/pyspark

And run the following command, which should also return 1000:

    >>> sc.parallelize(range(1000)).count()

## Example Programs

Spark also comes with several sample programs in the `examples` directory.
To run one of them, use `./bin/run-example <class> [params]`. For example:

    ./bin/run-example SparkPi

will run the Pi example locally.

You can set the MASTER environment variable when running examples to submit
examples to a cluster. This can be a mesos:// or spark:// URL,
"yarn" to run on YARN, and "local" to run
locally with one thread, or "local[N]" to run locally with N threads. You
can also use an abbreviated class name if the class is in the `examples`
package. For instance:

    MASTER=spark://host:7077 ./bin/run-example SparkPi

Many of the example programs print usage help if no params are given.

## Running Tests

Testing first requires [building Spark](#building-spark). Once Spark is built, tests
can be run using:

    ./dev/run-tests

Please see the guidance on how to
[run tests for a module, or individual tests](http://spark.apache.org/developer-tools.html#individual-tests).

## A Note About Hadoop Versions

Spark uses the Hadoop core library to talk to HDFS and other Hadoop-supported
storage systems. Because the protocols have changed in different versions of
Hadoop, you must build Spark against the same version that your cluster runs.

Please refer to the build documentation at
["Specifying the Hadoop Version"](http://spark.apache.org/docs/latest/building-spark.html#specifying-the-hadoop-version)
for detailed guidance on building for a particular distribution of Hadoop, including
building for particular Hive and Hive Thriftserver distributions.

## Configuration

Please refer to the [Configuration Guide](http://spark.apache.org/docs/latest/configuration.html)
in the online documentation for an overview on how to configure Spark.

## Contributing

Please review the [Contribution to Spark guide](http://spark.apache.org/contributing.html)
for information on how to get started contributing to the project.

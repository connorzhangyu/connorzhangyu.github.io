---
title: Flink
tags:
  - Java
  - 大数据
categories:
  - Java
cover: https://image.runtimes.cc/202404051527464.png
abbrlink: 3113
date: 2022-11-19 18:25:00
updated: 2022-11-19 18:25:00
---

# Flink

## 尝试flink

### 本地安装

步骤一:下载

为了能够运行Flink，唯一的要求是安装有效的**Java 8或11**。您可以通过发出以下命令来检查Java的正确安装：

```shell
# 要安装java环境
java -version

# 下载解压flink
tar -xzf flink-1.11.2-bin-scala_2.11.tgz
cd flink-1.11.2-bin-scala_2.11
```

步骤二:启动本地集群

```
$ ./bin/start-cluster.sh
Starting cluster.
Starting standalonesession daemon on host.
Starting taskexecutor daemon on host.
```

步骤三:提交一个job

```
$ ./bin/flink run examples/streaming/WordCount.jar
$ tail log/flink-*-taskexecutor-*.out
  (to,1)
  (be,1)
  (or,1)
  (not,1)
  (to,2)
  (be,2)
```

步骤四:停止集群

```
$ ./bin/stop-cluster.sh
```

## 使用DataStream API进行欺诈检测

### Java环境

```
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
		 xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>

	<groupId>frauddetection</groupId>
	<artifactId>frauddetection</artifactId>
	<version>0.1</version>
	<packaging>jar</packaging>

	<name>Flink Walkthrough DataStream Java</name>
	<url>https://flink.apache.org</url>

	<properties>
		<project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
		<flink.version>1.10.2</flink.version>
		<java.version>1.8</java.version>
		<scala.binary.version>2.11</scala.binary.version>
		<maven.compiler.source>${java.version}</maven.compiler.source>
		<maven.compiler.target>${java.version}</maven.compiler.target>
	</properties>

	<dependencies>
		<dependency>
			<groupId>org.apache.flink</groupId>
			<artifactId>flink-walkthrough-common_${scala.binary.version}</artifactId>
			<version>${flink.version}</version>
		</dependency>

		<!-- This dependency is provided, because it should not be packaged into the JAR file. -->
		<dependency>
			<groupId>org.apache.flink</groupId>
			<artifactId>flink-streaming-java_${scala.binary.version}</artifactId>
			<version>${flink.version}</version>
<!--			<scope>provided</scope>-->
		</dependency>

		<dependency>
			<groupId>org.projectlombok</groupId>
			<artifactId>lombok</artifactId>
			<version>1.18.0</version>
			<scope>provided</scope>
		</dependency>

		<!-- Add connector dependencies here. They must be in the default scope (compile). -->

		<dependency>
			<groupId>org.apache.flink</groupId>
			<artifactId>flink-connector-kafka-0.10_${scala.binary.version}</artifactId>
			<version>${flink.version}</version>
		</dependency>

		<!-- Add logging framework, to produce console output when running in the IDE. -->
		<!-- These dependencies are excluded from the application JAR by default. -->
		<dependency>
			<groupId>org.slf4j</groupId>
			<artifactId>slf4j-log4j12</artifactId>
			<version>1.7.7</version>
			<scope>runtime</scope>
		</dependency>
		<dependency>
			<groupId>log4j</groupId>
			<artifactId>log4j</artifactId>
			<version>1.2.17</version>
			<scope>runtime</scope>
		</dependency>
	</dependencies>

	<build>
		<plugins>

			<!-- Java Compiler -->
			<plugin>
				<groupId>org.apache.maven.plugins</groupId>
				<artifactId>maven-compiler-plugin</artifactId>
				<version>3.1</version>
				<configuration>
					<source>${java.version}</source>
					<target>${java.version}</target>
				</configuration>
			</plugin>

			<!-- We use the maven-shade plugin to create a fat jar that contains all necessary dependencies. -->
			<!-- Change the value of <mainClass>...</mainClass> if your program entry point changes. -->
			<plugin>
				<groupId>org.apache.maven.plugins</groupId>
				<artifactId>maven-shade-plugin</artifactId>
				<version>3.0.0</version>
				<executions>
					<!-- Run shade goal on package phase -->
					<execution>
						<phase>package</phase>
						<goals>
							<goal>shade</goal>
						</goals>
						<configuration>
							<artifactSet>
								<excludes>
									<exclude>org.apache.flink:force-shading</exclude>
									<exclude>com.google.code.findbugs:jsr305</exclude>
									<exclude>org.slf4j:*</exclude>
									<exclude>log4j:*</exclude>
								</excludes>
							</artifactSet>
							<filters>
								<filter>
									<!-- Do not copy the signatures in the META-INF folder.
                                    Otherwise, this might cause SecurityExceptions when using the JAR. -->
									<artifact>*:*</artifact>
									<excludes>
										<exclude>META-INF/*.SF</exclude>
										<exclude>META-INF/*.DSA</exclude>
										<exclude>META-INF/*.RSA</exclude>
									</excludes>
								</filter>
							</filters>
							<transformers>
								<transformer implementation="org.apache.maven.plugins.shade.resource.ManifestResourceTransformer">
									<mainClass>spendreport.FraudDetectionJob</mainClass>
								</transformer>
							</transformers>
						</configuration>
					</execution>
				</executions>
			</plugin>
		</plugins>

		<pluginManagement>
			<plugins>

				<!-- This improves the out-of-the-box experience in Eclipse by resolving some warnings. -->
				<plugin>
					<groupId>org.eclipse.m2e</groupId>
					<artifactId>lifecycle-mapping</artifactId>
					<version>1.0.0</version>
					<configuration>
						<lifecycleMappingMetadata>
							<pluginExecutions>
								<pluginExecution>
									<pluginExecutionFilter>
										<groupId>org.apache.maven.plugins</groupId>
										<artifactId>maven-shade-plugin</artifactId>
										<versionRange>[3.0.0,)</versionRange>
										<goals>
											<goal>shade</goal>
										</goals>
									</pluginExecutionFilter>
									<action>
										<ignore/>
									</action>
								</pluginExecution>
								<pluginExecution>
									<pluginExecutionFilter>
										<groupId>org.apache.maven.plugins</groupId>
										<artifactId>maven-compiler-plugin</artifactId>
										<versionRange>[3.1,)</versionRange>
										<goals>
											<goal>testCompile</goal>
											<goal>compile</goal>
										</goals>
									</pluginExecutionFilter>
									<action>
										<ignore/>
									</action>
								</pluginExecution>
							</pluginExecutions>
						</lifecycleMappingMetadata>
					</configuration>
				</plugin>
			</plugins>
		</pluginManagement>
	</build>
</project>
```

## 输入

### File输入

```
public static void main(String[] args) throws Exception {
    final StreamExecutionEnvironment env = StreamExecutionEnvironment.getExecutionEnvironment();
    final DataStream<String> stringDataStreamSource = env.readTextFile("Sensor.txt");
    stringDataStreamSource.print("data");
    env.execute();
}
```

## Kafka

需要引入包

```
<dependency>
    <groupId>org.apache.flink</groupId>
    <artifactId>flink-connector-kafka-0.10_${scala.binary.version}</artifactId>
    <version>${flink.version}</version>
</dependency>
```

```
public static void main(String[] args) throws Exception {
    final StreamExecutionEnvironment env = StreamExecutionEnvironment.getExecutionEnvironment();
    // 配置kafka,账号密码
    final Properties properties = new Properties();
    final DataStreamSource<String> sensor = env.addSource(new FlinkKafkaConsumer010<String>("sensor", new SimpleStringSchema(), properties));
    sensor.print("data");
    env.execute();
}
```

## 集合获取

```
public static void main(String[] args) throws Exception {

    final StreamExecutionEnvironment env = StreamExecutionEnvironment.getExecutionEnvironment();

    DataStream<SensorReading> dataStreamSource = env.fromCollection(Arrays.asList(
        new SensorReading("sen1", 1l, 37.5),
        new SensorReading("sen2", 2l, 38.5),
        new SensorReading("sen3", 3l, 39.5),
        new SensorReading("sen4", 4l, 40.5)));

    final DataStreamSource<Integer> integerDataStreamSource = env.fromElements(11, 12, 13, 14, 15);
    dataStreamSource.print("data");
    integerDataStreamSource.print("my list");
    env.execute();
}
```

## 输出

## 自定义数据源

模拟从kafka中获取

```
public static void main(String[] args) throws Exception {
    final StreamExecutionEnvironment env = StreamExecutionEnvironment.getExecutionEnvironment();
    // kafka配置
    final Properties properties = new Properties();
    // 加入自定义的数据源
    final DataStreamSource sensor = env.addSource(new MySensorce());
    sensor.print("data");
    env.execute();
}
```

自定义数据源

```
public class MySensorce implements SourceFunction<SensorReading> {

    private boolean running = true;

    @Override
    public void run(SourceContext<SensorReading> sourceContext) throws Exception {

        final Random random = new Random();

        final HashMap<String, Double> stringDoubleHashMap = new HashMap<>();

        for (int i = 1; i < 11; i++) {
            stringDoubleHashMap.put(i + "", random.nextGaussian());
        }

        while (running) {

            for (String id : stringDoubleHashMap.keySet()) {
                final double v = stringDoubleHashMap.get(id) + random.nextGaussian();
                final SensorReading sensorReading = new SensorReading(id, System.currentTimeMillis(), v);
                sourceContext.collect(sensorReading);
            }

            Thread.sleep(1000);

        }
    }

    @Override
    public void cancel() {
        running = false;
    }
}
```

## 算子

### 基本算子

#### map

```
final SingleOutputStreamOperator<Integer> map = inputStream.map(new MapFunction<String, Integer>() {
    @Override
    public Integer map(String s) throws Exception {
        return s.length();
    }
});
```

#### flatmap

```
final SingleOutputStreamOperator<String> stringSingleOutputStreamOperator = inputStream.flatMap(new FlatMapFunction<String, String>() {
    @Override
    public void flatMap(String s, Collector<String> collector) throws Exception {
        Arrays.stream(s.split(",")).forEach(collector::collect);
    }
});
```

#### filter

```
final SingleOutputStreamOperator<String> filter = inputStream.filter(new FilterFunction<String>() {
    @Override
    public boolean filter(String s) throws Exception {
        return s.startsWith("sen");
    }
});
```

### 分流,合流

```
public static void main(String[] args) throws Exception {

    final StreamExecutionEnvironment env = StreamExecutionEnvironment.getExecutionEnvironment();

    final DataStream<String> inputStream = env.readTextFile("Sensor.txt");

    final DataStream<SensorReading> map = inputStream.map(line -> {
        final String[] split = line.split(",");
        return new SensorReading(split[0], Long.parseLong(split[1]), Double.parseDouble(split[2]));
    });

    // 分流
    final SplitStream<SensorReading> split = map.split(new OutputSelector<SensorReading>() {
        @Override
        public Iterable<String> select(SensorReading value) {
            return value.getTemp() > 30 ? Collections.singletonList("hight") : Collections.singletonList("low");
        }
    });

    final DataStream<SensorReading> hight = split.select("hight");
    final DataStream<SensorReading> low = split.select("low");
    final DataStream<SensorReading> all = split.select("low","hight");

    hight.print("hight");
    low.print("low");
    all.print("all");

    final SingleOutputStreamOperator<Tuple2<String, Double>> warningStream = hight.map(new MapFunction<SensorReading, Tuple2<String, Double>>() {
        @Override
        public Tuple2<String, Double> map(SensorReading sensorReading) throws Exception {
            return new Tuple2<>(sensorReading.getId(), sensorReading.getTemp());
        }
    });

    final ConnectedStreams<Tuple2<String, Double>, SensorReading> connectedStreams = warningStream.connect(low);

    // 合流
    final SingleOutputStreamOperator<Object> result = connectedStreams.map(new CoMapFunction<Tuple2<String, Double>, SensorReading, Object>() {
        @Override
        public Object map1(Tuple2<String, Double> value) throws Exception {
            return new Tuple3<>(value.f0, value.f1, "高温报警");
        }

        @Override
        public Object map2(SensorReading value) throws Exception {
            return new Tuple2<>(value.getId(), "正常");
        }
    });

    // 第二种方法,ubion
    final DataStream<SensorReading> union = hight.union(low);

    result.print();
    union.print("ubion");
    env.execute();
}
```

### 富函数

```
public static void main(String[] args) throws Exception {

    final StreamExecutionEnvironment env = StreamExecutionEnvironment.getExecutionEnvironment();

    final DataStream<String> inputStream = env.readTextFile("Sensor.txt");

    final DataStream<SensorReading> map = inputStream.map(line -> {
        final String[] split = line.split(",");
        return new SensorReading(split[0], Long.parseLong(split[1]), Double.parseDouble(split[2]));
    });

    DataStream<Tuple2<String, Integer>> resultStream = map.map(new RichMyMapper());
    resultStream.print();
    env.execute();
}

public static class MyMapper implements MapFunction<SensorReading, Tuple2<String, Integer>> {
    @Override
    public Tuple2<String, Integer> map(SensorReading sensorReading) throws Exception {
        return new Tuple2<>(sensorReading.getId(), sensorReading.getId().length());
    }
}

// 自定义富函数
public static class RichMyMapper extends RichMapFunction<SensorReading, Tuple2<String, Integer>> {

    @Override
    public Tuple2<String, Integer> map(SensorReading value) throws Exception {
        // getRuntimeContext().getState();
        return new Tuple2<>(value.getId(), getRuntimeContext().getIndexOfThisSubtask());
    }

    @Override
    public void open(Configuration parameters) throws Exception {
        // 用来建立数据库连接
        super.open(parameters);
    }

    @Override
    public void close() throws Exception {
        // 关闭数据库
        super.close();
    }
}
```

### 分组,聚合

```
public static void main(String[] args) throws Exception {

    final StreamExecutionEnvironment env = StreamExecutionEnvironment.getExecutionEnvironment();

    final DataStream<String> inputStream = env.readTextFile("Sensor.txt");

    final DataStream<SensorReading> map = inputStream.map(line -> {
        final String[] split = line.split(",");
        return new SensorReading(split[0], Long.parseLong(split[1]), Double.parseDouble(split[2]));
    });

    // 分组
    //  final KeyedStream<SensorReading, Tuple> id = map.keyBy("id");
    final KeyedStream<SensorReading, String> keyedStream = map.keyBy(SensorReading::getId);

    // reduce
    final SingleOutputStreamOperator<SensorReading> reduce = keyedStream.reduce(new ReduceFunction<SensorReading>() {
        @Override
        public SensorReading reduce(SensorReading valu1, SensorReading valu2) throws Exception {
            return new SensorReading(valu1.getId(), valu2.getTimestamp(), Math.max(valu1.getTemp(), valu2.getTemp()));
        }
    });

    reduce.print();
    env.execute();
}
```


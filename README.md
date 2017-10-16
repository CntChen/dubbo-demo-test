# dubbo-demo-test
learning java. use dubbo with zookeeper.

## Environment
```bash
maven 3.5.0
java 1.8.0_144
dubbo 2.5.6
zookerper 3.4.10 inside docker
```

## Init
### fork
fork project from https://github.com/alibaba/dubbo/blob/dubbo-2.5.6/dubbo-demo.

### package to jar
jar is easier to run as a demo.

add following code to `configulation` filed of `maven-assembly-plugin` in file `dubbo-demo-provider/pom.xml` and `dubbo-demo-consumer/pom.xml`
```xml
<archive>
    <manifest>
        <mainClass>com.alibaba.dubbo.demo.provider.Provider</mainClass>
    </manifest>
</archive>
<descriptorRefs>
    <descriptorRef>jar-with-dependencies</descriptorRef>
</descriptorRefs>
```

### package
```bash
$ mvn package
```

## Start
### run
```
$ java -jar dubbo-demo-provider/target/dubbo-demo-provider-2.5.6-jar-with-dependencies.jar
```

### deal with multicase fail
* error
```bash
Exception in thread "main" java.lang.IllegalStateException: Can't assign requested address
        at com.alibaba.dubbo.registry.multicast.MulticastRegistry.<init>(MulticastRegistry.java:116)
...
```

* [how to fix this bug][how to fix this bug]: add `-Djava.net.preferIPv4Stack=true`

* new command

provider:
```bash
$ java -Djava.net.preferIPv4Stack=true -jar dubbo-demo-provider/target/dubbo-demo-provider-2.5.6-jar-with-dependencies.jar
...
==========
[17:05:30] Hello world, request from consumer: /10.75.94.35:58167
==========
```

consumer:
```bash
$ java -Djava.net.preferIPv4Stack=true -jar dubbo-demo-consumer/target/dubbo-demo-consumer-2.5.6-jar-with-dependencies.jar
...
==========
Hello world, response form provider: 10.75.94.35:20880
==========
```

## Work with zookeeper
### add zookeeper
change `dubbo:registry` in file `dubbo-demo-provider/src/main/resources/META-INF/spring/dubbo-demo-provider.xml` and `dubbo-demo-consumer/src/main/resources/META-INF/spring/dubbo-demo-consumer.xml`
```xml
<dubbo:registry address="zookeeper://127.0.0.1:2181"/>
```

remove comment in file `dubbo-demo-provider/src/main/assembly/conf/dubbo.properties` and `dubbo-demo-consumer/src/main/assembly/conf/dubbo.properties`
```
dubbo.registry.address=zookeeper://127.0.0.1:2181
```

### run test
don't need `-Djava.net.preferIPv4Stack=true` many more.
* providor
```bash
$ java -jar dubbo-demo-provider/target/dubbo-demo-provider-2.5.6-jar-with-dependencies.jar
...
==========
[17:05:30] Hello world, request from consumer: /10.75.94.35:58167
==========
```

* consumer
```bash
$ java dubbo-demo-consumer/target/dubbo-demo-consumer-2.5.6-jar-with-dependencies.jar
...
==========
Hello world, response form provider: 10.75.94.35:20880
==========
```


[#silent links]:#
[how to fix this bug]: https://stackoverflow.com/questions/18747134/getting-cant-assign-requested-address-java-net-socketexception-using-ehcache

## EOF
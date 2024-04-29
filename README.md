# Set up Kafka with Intellij idea

<div align="center">

![Java](https://img.shields.io/badge/Java-ED8B00?style=for-the-badge&logo=openjdk&logoColor=white)
![Apache Kafka](https://img.shields.io/badge/Apache%20Kafka-000?style=for-the-badge&logo=apachekafka)
![Apache Maven](https://img.shields.io/badge/Apache%20Maven-C71A36?style=for-the-badge&logo=Apache%20Maven&logoColor=white)
![Intellij Idea](https://img.shields.io/badge/IntelliJ_IDEA-000000.svg?style=for-the-badge&logo=intellij-idea&logoColor=white)
</div>

## I. Download latest version of Kafka from the official docs:
<a href="https://kafka.apache.org/downloads">Download</a>

## I.. Extract the file and run Zookeper and kafka-server

![Screenshot from 2024-04-29 10-25-40](https://github.com/Marouane-Elgoumiri/kafka_initiation/assets/96888594/69497d5a-2d57-447b-81a0-1fd6695be522)


### open the extracted folder:
```bash
   cd kafka_2.12-3.7.0
```

### run zookeper:
```bash
  bin/zookeeper-server-start.sh config/zookeeper.properties
```
### run Kafka-server client:

```bash
  bin/kafka-server-start.sh config/server.properties
```
## III. Create a maven project:

![Screenshot from 2024-04-29 10-27-58](https://github.com/Marouane-Elgoumiri/kafka_initiation/assets/96888594/ae282c0d-0dd1-420c-b589-c91b5a61c608)

### Add the necessary Dependencies from Apache Kafka docs (Client & Streams)

```bash
  <dependency>
      <groupId>org.apache.kafka</groupId>
      <artifactId>kafka-streams</artifactId>
      <version>3.7.0</version>
    </dependency>
    <dependency>
      <groupId>org.apache.kafka</groupId>
      <artifactId>kafka_2.13</artifactId>
      <version>3.7.0</version>
    </dependency>
    <dependency>
      <groupId>org.apache.kafka</groupId>
      <artifactId>kafka-clients</artifactId>
      <version>3.7.0</version>
    </dependency>
    <dependency>
      <groupId>org.slf4j</groupId>
      <artifactId>slf4j-log4j12</artifactId>
      <version>1.7.25</version>
    </dependency>
    <dependency>
      <groupId>log4j</groupId>
      <artifactId>log4j</artifactId>
      <version>1.2.17</version>
    </dependency>
```
### Create Producer class:
```java
  public class ProducerApp {
    public static void main(String[] args) {
        ProducerApp producerApp = new ProducerApp();
        producerApp.sendMessage();
    }

    public void sendMessage() {
        Properties props = new Properties();
        props.put("bootstrap.servers", "localhost:9092");
        props.put("acks", "all");
        props.put("retries", 0);
        props.put("key.serializer", "org.apache.kafka.common.serialization.StringSerializer");
        props.put("value.serializer", "org.apache.kafka.common.serialization.StringSerializer");
        props.put("GROUP_ID_CONFIG", "test-group-1");

        KafkaProducer<String, String> producer = new KafkaProducer<>(props);
        int i = 0;
        while(true){
            ProducerRecord<String, String> message = new ProducerRecord<>("test-topic", Integer.toString(i));
            producer.send(message);
            try {
                i++;
                System.out.println("message" + i);
                Thread.sleep(1000);
            }catch (Exception e) {
                System.out.println(e.getMessage());
            }
        }
    }
}
```

### Create Consumer class:

```java
  package org.example;

import org.apache.kafka.clients.consumer.ConsumerRecords;
import org.apache.kafka.clients.consumer.KafkaConsumer;

import java.time.Duration;
import java.util.Collections;
import java.util.Properties;
import java.util.concurrent.Executors;
import java.util.concurrent.TimeUnit;

public class ConsumerApp {
    public static void main(String[] args) {
        Properties props = new Properties();
        props.put("bootstrap.servers", "localhost:9092");
        props.put("key.serializer", "org.apache.kafka.common.serialization.StringSerializer");
        props.put("value.serializer", "org.apache.kafka.common.serialization.StringSerializer");
        props.put("GROUP_ID_CONFIG", "test-group-1");

        KafkaConsumer<String, String> consumer = new KafkaConsumer<>(props);
        consumer.subscribe(Collections.singletonList("test-topic"));
        Executors.newScheduledThreadPool(1).scheduleAtFixedRate(()-> {
            ConsumerRecords<String, String> records = consumer.poll(Duration.ofMillis(1000));
            records.forEach(record -> {
                System.out.println("Key "+record.key()+" value "+record.value()+" topic:"+record.topic());
            });
        },1000, 1000, TimeUnit.MILLISECONDS);

```

### Result:

![Screenshot from 2024-04-29 10-29-02](https://github.com/Marouane-Elgoumiri/kafka_initiation/assets/96888594/fea0ba49-015a-492d-b12e-cbf051c9cbd4)

        
  

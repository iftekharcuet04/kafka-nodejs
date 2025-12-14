# Kafka 1-Node (KRaft) – Local Development Guide

This setup runs a single Kafka broker in KRaft mode using Docker, along with a Node.js container for testing producers and consumers.

It is intended for learning, development, and local testing.

## Start Kafka

```docker compose up -d```


```docker exec -it kafka bash```

### Create a topic

#### Option A: 1 partition (simple)

``` kafka-topics \
  --bootstrap-server localhost:9092 \
  --create \
  --topic demo-topic \
  --partitions 1 \
  --replication-factor 1
  
  ```

#### Option B: Multiple partitions (parallelism)

``` kafka-topics \
  --bootstrap-server localhost:9092 \
  --create \
  --topic demo-topic \
  --partitions 3 \
  --replication-factor 1
```

Replication factor MUST be 1 (single broker).

#### Describe topic

``` kafka-topics \
  --bootstrap-server localhost:9092 \
  --describe \
  --topic demo-topic

```


#### Start Producer (Terminal 1)

``` kafka-console-producer \
  --bootstrap-server localhost:9092 \
  --topic demo-topic

```

Type messages:

``` hello
order-1
order-2

```


#### Start Consumer Group (Terminal 2)

``` kafka-console-consumer \
  --bootstrap-server localhost:9092 \
  --topic demo-topic \
  --group my-group \
  --from-beginning

```

#### Start Another Consumer (Terminal 3)

``` kafka-console-consumer \
  --bootstrap-server localhost:9092 \
  --topic demo-topic \
  --group my-group
```

### What happens?
##### If topic has 1 partition

* Consumer-1 → ACTIVE

* Consumer-2 → IDLE

##### If topic has 3 partitions

* Up to 3 consumers active

* Extra consumers → IDLE

✅ This is by design, not a bug.


#### Multiple Consumer Groups (important)

``` kafka-console-consumer \
  --bootstrap-server localhost:9092 \
  --topic demo-topic \
  --group another-group
``` 

✅ Both groups receive all messages independently.

### 🔁 Rebalance behavior (your earlier issue)

###### If you force close a consumer:

Kafka waits for session timeout

Other consumers appear idle

After timeout → rebalance → new consumer becomes active

Correct way to exit:

```CTRL + C```









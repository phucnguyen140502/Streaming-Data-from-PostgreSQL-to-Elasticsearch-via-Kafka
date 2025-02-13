# Streaming Data from PostgreSQL to Elasticsearch via Kafka

## 1. Introduction

This system is deployed on a **VPS (Virtual Private Server)** without Docker, aiming to improve query performance from a **PostgreSQL table containing 5,000,000 products**. Since querying PostgreSQL directly takes too much time, we utilize **Kafka to stream data** into Elasticsearch, enabling faster queries through API calls.

## 2. System Architecture

![System Architecture](https://github.com/user-attachments/assets/fb411457-2102-4fff-a8ff-569db837ac2c)

The system consists of the following components:

- **PostgreSQL**: Stores the `elasticsearch_product` table containing product data.
- **Debezium**: CDC (Change Data Capture) tool to monitor changes in PostgreSQL and send them to Kafka.
- **Kafka**: Receives changes from PostgreSQL and forwards them to consumers.
- **Logstash**: Kafka consumer that extracts data and pushes it into Elasticsearch.
- **Elasticsearch**: Provides fast search capabilities using REST APIs.

## 3. System Configuration

### PostgreSQL Configuration

To enable Debezium, **logical replication** must be enabled in PostgreSQL:

```sql
ALTER SYSTEM SET wal_level = logical;
ALTER SYSTEM SET max_replication_slots = 5;
ALTER SYSTEM SET max_wal_senders = 5;
SELECT pg_reload_conf();
```
Create a replication slot:

```sql
SELECT * FROM pg_create_logical_replication_slot('debezium_slot', 'pgoutput');
```

Create the product table:

```sql
CREATE TABLE elasticsearch_product (
    id SERIAL PRIMARY KEY,
    product_name TEXT,
    brand TEXT,
    category TEXT,
    cpu TEXT,
    gpu TEXT,
    price NUMERIC
);

select count(*) from elasticsearch_product;
```
![image](https://github.com/user-attachments/assets/80dcd8fe-511a-422c-af62-d292bd60a4b8)

### Debezium Configuration

#### Debezium connects PostgreSQL and Kafka, sending data changes to Kafka.
![image](https://github.com/user-attachments/assets/8d2c672e-faa2-424a-bc37-6d26e25bf322)

### Logstash Configuration for Pushing Kafka Data to Elasticsearch
#### Logstash configuration file:
![image](https://github.com/user-attachments/assets/faaedee0-08c8-444b-839a-8709f5f8f3fb)

#### Start Logstash:
```
systemctl restart logstash 
```
--> I set logstach such service on linux

### 4. Query Performance Comparison: PostgreSQL vs. Elasticsearch

### Querying PostgresSQL
![image](https://github.com/user-attachments/assets/7b9ba351-5d43-4a68-b2de-2c2fd861841c)

##### Results:
- Rows Removed by Filter: 4,649,750
- Execution Time: 17,306362 ms (~17 seconds)

### Querying Elasticsearch
![image](https://github.com/user-attachments/assets/e4dd5ad5-4722-43ad-bd48-8bfba2d8ef1b)

#### Results:
- Execution Time: 286 ms (~0.3 seconds)

### 5. Conclusion

| System         | Query Time       |
|---------------|-----------------|
| PostgreSQL    | 17,036 ms (~17s) |
| Elasticsearch | 286 ms (~0.3s)   |

> **Elasticsearch reduces query time by more than 50 times compared to PostgreSQL.**  
> This demonstrates the **effectiveness of using Kafka** to stream data from PostgreSQL to Elasticsearch,  
> significantly enhancing query performance and scalability.

# Tech E-Commerce System

## 1. Context

This project involves developing an e-commerce platform specializing in tech products, including laptops, mobile devices, and other electronic gadgets. With a database containing approximately **5,000,000 products**, direct querying using PostgreSQL can lead to significant latency when users search for products by keywords, ultimately impacting user experience.
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

![image](https://github.com/user-attachments/assets/e25bc407-e8a4-460d-977f-1cafbddb90ce)

---

## 2. Proposed Solution
To address performance issues during search operations, we integrated **Elasticsearch** as a dedicated search engine. The solution involves synchronizing product data from our core web system (stored in PostgreSQL) to Elasticsearch, ensuring rapid and efficient keyword-based search queries.

---

## 3. Implementation

- **Primary Database:**  
  - **PostgreSQL:** Serves as the main data store, ensuring data integrity and consistency across product information.
```sql
SELECT * FROM pg_create_logical_replication_slot('debezium_slot', 'pgoutput');


ALTER SYSTEM SET wal_level = logical;
ALTER SYSTEM SET max_replication_slots = 5;
ALTER SYSTEM SET max_wal_senders = 5;
SELECT pg_reload_conf();
```

- **Elasticsearch Integration:**  
  - Configured Elasticsearch to handle incoming search queries from users.
  - Implemented an automatic synchronization mechanism to transfer and update product data from PostgreSQL to Elasticsearch using tools such as Logstash or custom ETL scripts.

![image](https://github.com/user-attachments/assets/ddb0d925-ed68-47cf-8c6b-a226d5278c54)

![image](https://github.com/user-attachments/assets/a3871bdb-5a97-4cd0-bbeb-723fdb1ef6d2)

- **Data Synchronization Process:**  
  1. **Data Updates:** Product information is continuously updated in the core system.
  2. **Synchronization:** An automated service transfers updated data from PostgreSQL to Elasticsearch.
  3. **Search Queries:** User search requests are processed by Elasticsearch, which returns results swiftly and accurately.

The Flow Chart to apply:

![image](https://github.com/user-attachments/assets/44c09a81-70dd-4d93-a559-8cb019cb51a5)
---

## 4. Performance Evaluation

- **Testing and Benchmarking:**  
  - Measured response times of search queries executed on Elasticsearch versus direct PostgreSQL queries.
  - Evaluated search result accuracy and system scalability as the product count increases.

- **System Monitoring:**  
  - Employed monitoring tools to track the performance of both PostgreSQL and Elasticsearch.
  - Conducted periodic audits to ensure data consistency between the core system and Elasticsearch.

![image](https://github.com/user-attachments/assets/040f055a-98db-446a-a4bd-bbd4fd46b78e)

![image](https://github.com/user-attachments/assets/84a4ae80-d657-4d57-8b9f-9eda691e6318)

---

## 5. Conclusion

Integrating Elasticsearch into the tech e-commerce platform significantly enhanced search performance and improved user experience. The automatic data synchronization between PostgreSQL and Elasticsearch ensures that search results remain both accurate and up-to-date. This solution not only addresses current performance challenges but also provides a scalable foundation for future growth as the product catalog and user base expand.

*This project demonstrates the effective application of advanced search technologies to optimize performance in large-scale e-commerce systems.*

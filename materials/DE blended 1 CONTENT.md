# DE Blended 1: Apache Spark та Альтернативи (Розширена версія)

---

## 🔧 Основні варіанти розгортання Apache Spark

| Варіант                                      | Опис                                                                              | Переваги                                                                                                              | Недоліки                                                                                                  | Вартість |
| -------------------------------------------- | --------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------- | -------- |
| **1. Standalone Mode**                       | Spark запускається самостійно, з вбудованим кластерним менеджером.                | Простий запуск<br>Добре підходить для локальних кластерів або невеликих проєктів<br>Не потребує зовнішніх компонентів | Погано масштабується<br>Відсутність гнучкого керування ресурсами<br>Менше інтеграцій                      | 💲       |
| **2. YARN (Hadoop)**                         | Spark запускається на Hadoop-кластері, використовуючи YARN як ресурсний менеджер. | Добре інтегрується з екосистемою Hadoop<br>Широко використовується у великих організаціях                             | Складна інфраструктура<br>Застаріваюча модель у порівнянні з Kubernetes                                   | 💲💲     |
| **3. Mesos** ⚠️ *Deprecated*                 | Менш популярна система керування ресурсами.                                       | Гнучкий розподіл ресурсів                                                                                             | Застаріває, неактивний розвиток                                                                           | 💲💲     |
| **4. Docker**                                | Spark розгортається в контейнерах.                                                | Легкий девелопмент і CI/CD<br>Контроль над середовищем                                                                | Контейнери самі по собі не забезпечують кластеризацію — треба запускати Spark standalone або з Kubernetes | 💲       |
| **5. Kubernetes (k8s)**                      | Spark запускається як кластер на Kubernetes.                                      | Автоматичне масштабування<br>Краща ізоляція та керованість<br>Підходить для хмар і мікросервісів                      | Потрібні знання Kubernetes<br>Більше налаштувань                                                          | 💲💲     |
| **6. Cloud Native (EMR, Dataproc, Synapse)** | Spark як сервіс у хмарі (AWS EMR, GCP Dataproc, Azure Synapse).                   | Мінімум налаштувань<br>Інтеграція з іншими хмарними сервісами<br>Автоматичне масштабування                            | Дорожче<br>Vendor lock-in<br>Менше контролю                                                               | 💲💲💲    |
| **7. Local Mode**                            | Spark запускається на одному ноутбуку або сервері без кластеру.                   | Ідеально для розробки/навчання<br>Немає потреби в кластері                                                            | Немає масштабованості                                                                                     | Безкоштовно |
| **8. Serverless** 🆕                         | AWS Glue, Azure Synapse Serverless, GCP Dataflow                                 | Автоматичне масштабування<br>Платіш за використання<br>Нульове адміністрування                                        | Холодний старт<br>Обмежена кастомізація<br>Vendor lock-in                                                 | 💲💲💲💲   |

---

## 💰 TCO (Total Cost of Ownership) Порівняння

| Розгортання     | Infrastructure | Management | Development | Scalability | Загальна складність |
|----------------|----------------|------------|-------------|-------------|-------------------|
| Local/Docker   | Низька         | Низька     | Висока      | Погана      | ⭐⭐ |
| Standalone     | Середня        | Середня    | Висока      | Середня     | ⭐⭐⭐ |
| Kubernetes     | Висока         | Висока     | Середня     | Відмінна    | ⭐⭐⭐⭐ |
| Cloud Managed  | Низька         | Низька     | Середня     | Відмінна    | ⭐⭐ |
| Serverless     | Нульова        | Нульова    | Середня     | Автоматична | ⭐ |

---

## 📊 Рекомендації за обсягом даних

| Обсяг даних    | Spark                    | Краща альтернатива        | Обґрунтування |
|----------------|--------------------------|---------------------------|---------------|
| < 1GB          | ❌ Overkill              | DuckDB, Polars, Pandas   | Single-node достатньо |
| 1GB-100GB      | ✅ Local/Standalone      | Dask, Ray, DuckDB         | Локальна обробка ефективніша |
| 100GB-1TB      | ✅ Small cluster         | Spark, Trino, ClickHouse | Розподілена обробка починає окупатися |
| 1TB-10TB       | ✅ Medium cluster        | Spark + Delta, Trino      | Sweet spot для Spark |
| 10TB-100TB     | ✅ Large cluster         | Spark + Iceberg, Flink    | Потрібна оптимізація I/O |
| > 100TB        | ✅ Multi-cluster/Cloud   | Trino, Spark + Lakehouse | Федеровані запити, партиціонування |

---

## 👥 Рекомендації за навичками команди

| Команда має досвід в       | Рекомендація              | Чому |
|---------------------------|---------------------------|------|
| **Java/Scala**            | Spark, Flink, Trino      | Нативна мова, краща продуктивність |
| **Python Data Science**   | Ray, Dask, Modin, Polars | Інтеграція з ML екосистемою |
| **SQL Analysts**          | Trino, ClickHouse, DuckDB| Знайомий SQL інтерфейс |
| **DevOps/Kubernetes**     | Flink on K8s, Ray        | Використання наявних навичок |
| **Frontend Developers**   | Serverless (Glue, etc.)  | Мінімум інфраструктури |
| **Стартап (малі команди)**| DuckDB, Polars, Cloud    | Фокус на продукт, не на інфру |

---

## 🧩 Де може стояти Spark у архітектурі системи?

**Apache Spark** — universal compute engine, що може застосовуватися на різних рівнях:

### 1. **Data Ingestion & ETL**
* Використовується для масового перетворення, очищення, об'єднання даних.
* Читає з Kafka, S3, HDFS, Redpanda, баз даних.
* Приклад: парсинг логів, трансформація сирих даних.
```
Raw Data → Spark → Clean Data
├── Kafka/Kinesis streams
├── S3/ADLS/GCS files  
├── Databases (JDBC)
└── APIs & webhooks
```

### 2. **Batch Analytics Pipeline**
* Аналітична обробка великих обсягів даних.
* Приклад: щоденний підрахунок метрик, тренування ML-моделей.
```
Data Lake → Spark → Metrics/Reports → Dashboard
├── Daily aggregations
├── Complex joins
├── Business logic
└── ML feature engineering
```

### 3. **Streaming Architecture**
* Обробка потоку подій у реальному часі через `Spark Structured Streaming`.
* Приклад: аналіз поведінки користувачів у реальному часі.
```
Events → Kafka → Spark Streaming → Output
├── Near real-time (seconds)
├── Windowed aggregations
├── Complex event processing
└── ML inference pipelines
```

### 4. **Lakehouse Pattern**
* Побудова гібридної архітектури для аналітики, яка поєднує гнучкість data lake з надійністю та структурою data warehouse.
* Дані проходять через три основні шари, що забезпечують їх поступове очищення та збагачення:
    * Bronze: Сирі дані, збережені у вихідному форматі (наприклад, з Spark batch).
    * Silver: Очищені та відформатовані дані, готові для аналізу (Spark ETL).
    * Gold: Агреговані та готові для використання дані, оптимізовані для бізнес-аналітики (Spark/Trino).
```
Raw → Bronze → Silver → Gold
├── Bronze: Raw data (Spark batch)
├── Silver: Cleaned (Spark ETL)
├── Gold: Aggregated (Spark/Trino)
└── Serving: BI tools + APIs
```

### 5. **ML Pipeline Integration**
* Можна тренувати моделі через `MLlib` або інші бібліотеки.
* Приклад: кластеризація, рекомендаційні системи, anomaly detection.
```
Data → Feature Store → Training → Serving
├── MLlib for basic ML
├── Integration with MLflow
├── Feature engineering
└── Batch prediction
```

---

## 🔁 Альтернативи Apache Spark: повний огляд

### 🟠 Universal Compute Engines

| Технологія       | Матурність | Performance | Складність | Use Case | Статус |
|-----------------|-----------|-------------|------------|----------|--------|
| **Apache Flink** | Висока ⭐⭐⭐⭐ | Відмінна ⚡⚡⚡⚡ | Висока 🔧🔧🔧🔧 | True real-time | ✅ Активний |
| **Apache Beam**  | Висока ⭐⭐⭐⭐ | Залежить від runner | Висока 🔧🔧🔧🔧 | Portable pipelines | ✅ Активний |
| **Ray**          | Середня ⭐⭐⭐ | Відмінна ⚡⚡⚡⚡ | Середня 🔧🔧🔧 | ML + distributed | 🔥 Швидко росте |
| **Dask**         | Висока ⭐⭐⭐⭐ | Добра ⚡⚡⚡ | Низька 🔧🔧 | Python ecosystem | ✅ Стабільний |

### 🟡 Streaming Specialists

| Технологія        | Latency    | Throughput | SQL Support | Stateful | Community |
|------------------|------------|------------|-------------|----------|-----------|
| **Kafka Streams** | ms ⚡⚡⚡   | Висока ⬆️⬆️⬆️ | ❌ | ✅ | Велика |
| **ksqlDB**        | ms-sec ⚡⚡ | Висока ⬆️⬆️⬆️ | ✅ | ✅ | Середня |
| **Materialize**   | ms ⚡⚡⚡   | Середня ⬆️⬆️ | ✅ | ✅ | Мала |
| **Redpanda WASM** | μs ⚡⚡⚡⚡ | Дуже висока ⬆️⬆️⬆️⬆️ | ❌ | ✅ | Нова |
| **Bytewax**       | ms ⚡⚡⚡   | Середня ⬆️⬆️ | ❌ | ✅ | Мала |

### 🟢 OLAP & Analytics Engines

| Технологія            | Query Speed | Data Size | SQL Compatibility | Cloud Native |
|----------------------|-------------|-----------|-------------------|--------------|
| **Trino (Presto)**   | Швидка ⚡⚡⚡ | PB scale | ANSI SQL ✅ | ✅ |
| **ClickHouse**        | Дуже швидка ⚡⚡⚡⚡ | TB scale | ClickHouse SQL | Частково |
| **DuckDB**            | Швидка ⚡⚡⚡ | GB-TB scale | ANSI SQL ✅ | Embedded |
| **DataFusion**        | Швидка ⚡⚡⚡ | GB-TB scale | SQL subset | ✅ |
| **Velox (Meta)**      | Дуже швидка ⚡⚡⚡⚡ | TB scale | Via integrations | Розробляється |

### 🆕 Emerging Technologies

| Технологія   | Особливості | Статус | Перспективи |
|-------------|-------------|---------|-------------|
| **Polars**   | Rust-based DataFrame, швидший за Pandas | 🔥 Активний | Заміна Pandas |
| **Ballista** | Rust-based distributed compute | 🚧 Розробка | DataFusion backend |
| **Rath**     | Visual data analysis | 🆕 Нова | Low-code BI |

---

## 🔗 Ecosystem Integration Matrix

| Технологія | Kafka | S3/Object Store | Delta Lake | Iceberg | Hudi | Parquet | Arrow | JDBC |
|-----------|-------|-----------------|------------|---------|------|---------|-------|------|
| **Spark**     | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |
| **Flink**     | ✅ | ✅ | ❌ | ✅ | ❌ | ✅ | ❌ | ✅ |
| **Ray**       | ✅ | ✅ | Через Spark | ❌ | ❌ | ✅ | ✅ | ✅ |
| **Trino**     | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ❌ | ✅ |
| **ClickHouse**| ✅ | ✅ | ❌ | ❌ | ❌ | ✅ | ❌ | ✅ |
| **DuckDB**    | Через extension | ✅ | ❌ | ❌ | ❌ | ✅ | ✅ | ✅ |

---

## 🏗️ Hybrid Architecture Patterns

### Pattern 1: Multi-Engine Lakehouse
```
Ingestion: Kafka → Flink → Delta Lake
Batch ETL: Spark → Delta Lake  
Analytics: Trino → Delta Lake
Serving: DuckDB/ClickHouse
```

### Pattern 2: Speed Layer Architecture
```
Real-time: Kafka → Flink → Redis/ClickHouse
Batch: S3 → Spark → Data Warehouse
Serving: API Gateway → Multiple backends
```

### Pattern 3: ML-focused Pipeline
```
Data: S3 → Spark (feature eng.) → Feature Store
Training: Ray → MLflow → Model Registry  
Inference: Ray Serve / Spark Structured Streaming
```

### Pattern 4: Cost-Optimized Cloud
```
Ingestion: Managed Kafka → Serverless Functions
Processing: Spot Instances + Spark
Storage: S3 Intelligent Tiering
Analytics: On-demand Trino/Athena
```

---

## 🔄 Migration Strategy: від Spark до альтернатив

### Поетапна міграція

| Фаза | Компонент | З чого | На що | Складність |
|------|-----------|--------|--------|-----------|
| **1** | Batch ETL | Spark | Trino/DuckDB | 🟡 Середня |
| **2** | Streaming | Spark Streaming | Flink/Kafka Streams | 🔴 Висока |
| **3** | ML Pipelines | MLlib | Ray/scikit-learn | 🟡 Середня |
| **4** | Ad-hoc Analytics | Spark SQL | Trino/ClickHouse | 🟢 Низька |

### Compatibility Matrix для міграції

| Spark Component | Direct Replacement | Requires Rewrite | Migration Effort |
|----------------|-------------------|------------------|------------------|
| **Spark SQL** | Trino, DuckDB | ✅ | 1-2 weeks |
| **DataFrames** | Dask, Polars | ✅ | 2-4 weeks |
| **MLlib** | scikit-learn, Ray | ❌ | 1-3 months |
| **Streaming** | Flink, Kafka Streams | ❌ | 2-6 months |
| **GraphX** | NetworkX, igraph | ❌ | 1-4 months |

---

## ⚡ Performance Benchmarks (орієнтовні)

### Batch Processing (1TB data)
```
Spark (optimized):     45 min
Trino:                 35 min  
ClickHouse:            15 min (columnar data)
DuckDB:               25 min (single node)
Flink:                40 min
```

### Streaming Latency
```
Spark Streaming:       2-10 seconds
Flink:                100ms - 1s
Kafka Streams:        10-100ms
ksqlDB:               100ms - 1s  
Materialize:          10-50ms
```

### SQL Query Performance (100GB data)
```
Trino:                5-30 seconds
ClickHouse:           1-5 seconds
DuckDB:              10-60 seconds
Spark SQL:           15-120 seconds
```

---

## 🎯 Коли використовувати кожну технологію

### ✅ Залишайтесь з Spark коли:
- Вже маєте налаштований Spark кластер
- Команда має експертизу в Scala/Java
- Потрібна універсальність (batch + streaming + ML)
- Велика кількість legacy код
- Hadoop ecosystem integration

### 🔄 Варто розглянути альтернативи коли:

| Сценарій | Чому Spark не ідеальний | Краща альтернатива |
|----------|-------------------------|-------------------|
| **Real-time analytics** | Micro-batching затримка | Flink, Materialize |
| **Single-node analytics** | Overhead кластеру | DuckDB, ClickHouse |
| **Python ML workflows** | Обмежений MLlib | Ray, Dask |
| **Interactive SQL** | Повільний cold start | Trino, ClickHouse |
| **Small team/startup** | Operational complexity | Serverless, DuckDB |
| **Cost optimization** | Always-on clusters | Pay-per-query solutions |

---

## 🗺️ Technology Roadmap & Trends

### 📈 Зростаючі технології (2024-2025)
- **Ray**: ML workloads, distributed Python
- **Polars**: DataFrame processing
- **DuckDB**: Embedded analytics  
- **ClickHouse**: Real-time OLAP
- **Serverless compute**: Pay-per-use models

### 📉 Технології що занепадають
- **Apache Mesos**: Неактивний розвиток
- **MapReduce**: Застарілий підхід
- **Storm**: Заміщений Flink
- **Yarn-first deployments**: К8s стає стандартом

### 🔮 Майбутні тренди
- **WebAssembly in data processing**: Портабельність + performance
- **GPU-accelerated analytics**: Rapids, BlazingSQL
- **Quantum-ready algorithms**: Приготування до quantum computing
- **Edge analytics**: Обробка даних closer to source

---

## 📋 Decision Framework

### Питання для self-assessment:

1. **Data Volume**: < 1GB | 1-100GB | 100GB-1TB | > 1TB ?
2. **Latency Requirements**: Batch | Near real-time | Real-time | Interactive ?
3. **Team Skills**: Java/Scala | Python | SQL | DevOps ?
4. **Budget**: Cost-sensitive | Balanced | Performance-first ?
5. **Operational Complexity**: Minimal ops | Managed services | Full control ?

### Decision Tree:
```
Latency = Real-time? → Flink, Kafka Streams
├─ Data < 100GB? → DuckDB, ClickHouse  
├─ Python-first? → Ray, Dask
├─ SQL-heavy? → Trino, ClickHouse
├─ Cost-sensitive? → Serverless, Spot instances
└─ Established team? → Keep Spark, optimize
```

---

## 📚 Рекомендовані ресурси для поглибленого вивчення

### Офіційна документація
- [Apache Spark Documentation](https://spark.apache.org/docs/latest/)
- [Apache Flink Documentation](https://flink.apache.org/docs/stable/)
- [Ray Documentation](https://docs.ray.io/en/latest/)

### Benchmarks & Comparisons  
- [TPC-DS Benchmarks](http://www.tpc.org/tpcds/)
- [ClickBench](https://benchmark.clickhouse.com/)
- [DuckDB vs Alternatives](https://duckdb.org/why_duckdb)

### Community & Learning
- [Databricks Academy](https://academy.databricks.com/)
- [Confluent Kafka Academy](https://developer.confluent.io/)
- [Ray Summit](https://raysummit.anyscale.com/)

---

*Документ оновлено: вересень 2025. Технології швидко розвиваються, регулярно перевіряйте актуальність інформації.*
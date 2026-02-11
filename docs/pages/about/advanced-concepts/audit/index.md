# Audit

FF4j comes with audit which allows you to track and log feature usage, providing insights into how
features are being utilized in your application. This can be particularly useful for monitoring the
impact of feature toggles and making informed decisions about enabling or disabling features.

## Overview

If you have designed your code to isolate features (which pretty on-trend nowadays, promoting by
Agile development mode and epic/stories) and use FF4j to perform toggle you have an opportunity.
FF4j can log any feature invocation to help you counting hit ratio (per features, sources of
customers) or building usage histograms.

To implement these audit capabilities simply invoke those lines:

```java title="Snippet for enabling audit in FF4j"
ff4j.setEventRepository(<HERE YOUR EVENT_REPOSITORY DEFINITION>);
ff4j.audit(true);
```

Under the hood it FF4j will create a "AuditProxy" pretty static

```java
if (isEnableAudit()) {
  if (fstore != null && !(fstore instanceof FeatureStoreAuditProxy)) {
   this.fstore = new FeatureStoreAuditProxy(this, fstore);
  }
  if (pStore != null && !(pStore instanceof PropertyStoreAuditProxy)) { 
   this.pStore = new PropertyStoreAuditProxy(this, pStore);
  }
}
```

Most of the stores provide implementation for Audit, but sometimes it does not make sense to store
timeseries in target DB

## Sample

Some examples of audit implementation for various store as showcased below:

```java
// JDBC
HikariDataSource hikariDataSource;
ff4j.setEventRepository(new EventRepositorySpringJdbc(hikariDataSource));

// ELASTICSEARCH
URL urlElastic = new URL("http://" + elasticHostName + ":" + elasticPort);
ElasticConnection connElastic = new ElasticConnection(ElasticConnectionMode.JEST_CLIENT, elasticIndexName, urlElastic);
ff4j.setEventRepository(new EventRepositoryElastic(connElastic));

// REDIS
RedisConnection redisConnection = new RedisConnection(redisHostName, redisPort, redisPassword);
ff4j.setEventRepository(new EventRepositoryRedis(redisConnection ));

// MONGODB
MongoClient mongoClient;
ff4j.setEventRepository(new EventRepositoryMongo(mongoClient, mongoDatabaseName));

// CASSANDRA
Cluster cassandraCluster;
CassandraConnection cassandraConnection = new CassandraConnection(cassandraCluster)
ff4j.setEventRepository(new EventRepositoryCassandra(cassandraConnection));
```


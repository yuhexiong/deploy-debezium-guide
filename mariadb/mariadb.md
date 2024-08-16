# MariaDB Debezium

get cdc from MariaDB.  

## MariaDB
- config
usually at `/etc/mysql/my.cnf`, refer to [my.cnf](./my.cnf)

- example data
```
pc_id|model   |manufacturer      |location      |
-----+--------+------------------+--------------+
    1|IPC-A123|Advantech         |Factory Floor |
    2|IPC-B456|Siemens           |Warehouse     |
    3|IPC-C789|Beckhoff          |Control Room  |
    4|IPC-D012|Rockwell          |Assembly Line |
    5|IPC-E345|Schneider Electric|Packaging Area|
```


## Deploy

- Start
```
POST http://localhost:8083/connectors/
```
with body
```
{
  "name": "mariadb-connector",
  "config": {
    "connector.class": "io.debezium.connector.mysql.MySqlConnector",
    "database.hostname": "hostname",
    "database.port": "3306",
    "database.user": "root",
    "database.password": "",
    "database.server.name": "mariadb_server",
    "database.include.list": "pc",
    "table.include.list": "pc.industrial_pcs",
    "schema.history.internal.kafka.bootstrap.servers": "localhost:9092",
    "schema.history.internal.kafka.topic": "pc_topic",
    "include.schema.changes": "true",
    "database.server.id": "1",
    "snapshot.mode": "initial",
    "topic.prefix": "pc"
  }
}
```

- Result
On Kafka
```json
{
  "schema": {
    "type": "struct",
    "fields": [
      ...
    ],
    "optional": false,
    "name": "pc.pc.industrial_pcs.Envelope",
    "version": 1
  },
  "payload": {
    "before": null,
    "after": {
      "pc_id": 5,
      "model": "IPC-E345",
      "manufacturer": "Schneider Electric",
      "location": "Packaging Area"
    },
    "source": {
      "version": "2.5.4.Final",
      "connector": "mysql",
      "name": "pc",
      "ts_ms": 1722932442000,
      "snapshot": "false",
      "db": "pc",
      "sequence": null,
      "table": "industrial_pcs",
      "server_id": 1,
      "gtid": null,
      "file": "mysql-bin.000001",
      "pos": -239,
      "row": 4,
      "thread": null,
      "query": null
    },
    "op": "c",
    "ts_ms": 1722932505469,
    "transaction": null
  }
}
```

# Basic Setup

## Installation in Linux

#### Install java

```bash
sudo apt install openjdk-8-headless
```

#### Add the JRE into an environment variable

```bash
sudo vi ~/.bashrc
```

#### Add the line at the end of .bashrc

JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64

```bash
source ~/.bashrc
```

#### Verify settings

```bash
echo $JAVA_HOME
```

alias nt='JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64/ nodetool'

This error may occur when using the ‘nodetool’ command, such as in the following example:

nodetool status

For both CentOS 6 and CentOS 7 search the following configuration file:

vim /etc/cassandra/default.conf/cassandra-env.sh

Search for:

JVM_OPTS="$JVM_OPTS -Djava.rmi.server.hostname=

Which for my search resulted in:

add this if you’re having trouble connecting:
JVM_OPTS=”$JVM_OPTS -Djava.rmi.server.hostname=<public name>”

Uncomment the second line, and add the hostname of your server, or the IP address which you’re connecting to/from. In this case, replacing <public name> with 127.0.0.1 resolved the issue, because I am connecting to/from that IP address.

Then exit and save the file with the command :wq .

Be sure to restart Cassandra:

systemctl restart cassandra

#### install curl

```bash
sudo apt install curl
```

#### Follow these commands

[how-to-install-apache-cassandra-3-11-x-on-debian-9](https://www.vultr.com/docs/how-to-install-apache-cassandra-3-11-x-on-debian-9)

#### Start the Apache Cassandra

```bash
sudo service cassandra start
```

#### Check the status

```bash
sudo service cassandra status
```

#### Check the status of nodes

```bash
nodetool status
```

#### Use the cqlsh shell to interact with Apache Cassandra Python (2.7+ < 3.0)

```bash
cqlsh
```

#### Check the rack and datacentres of Cassandra

```bash
cd /etc/cassandra # location of cassandra

 vi cassandra-rackdc.properties
```

#### Cassandra shell

```bash
cqlsh
```

#### Create a keyspace

```cql
CREATE KEYSPACE test_keyspace WITH replication = {'class': 'SimpleStrategy', 'replication_factor': '1'} AND durable_writes = 'true';
```

##### Verify creation of keyspace

```cql
DESCRIBE KEYSPACES;
```

#### Use keyspace

```cql
USE test_keyspace;
```

#### Create a table in the keyspace

```cql
CREATE TABLE employee_by_id (id int PRIMARY KEY,
... name text,
... position text);
```

#### Check the creation table

```cql
DESCRIBE TABLES;
```

#### Create a table in the keyspace

```cql
CREATE TABLE employee_by_car_make
                 ... ( car_make text,
                 ... id int,
                 ... car_model text,
                 ... PRIMARY KEY(car_make, id));
```

#### Check the creation table

```cql
DESCRIBE TABLE employee_by_car_make;
```

```bash
CREATE TABLE test_keyspace.employee_by_car_make (
    car_make text,
    id int,
    car_model text,
    PRIMARY KEY (car_make, id)
) WITH CLUSTERING ORDER BY (id ASC)
    AND bloom_filter_fp_chance = 0.01
    AND caching = {'keys': 'ALL', 'rows_per_partition': 'NONE'}
    AND comment = ''
    AND compaction = {'class': 'org.apache.cassandra.db.compaction.SizeTieredCompactionStrategy', 'max_threshold': '32', 'min_threshold': '4'}
    AND compression = {'chunk_length_in_kb': '64', 'class': 'org.apache.cassandra.io.compress.LZ4Compressor'}
    AND crc_check_chance = 1.0
    AND dclocal_read_repair_chance = 0.1
    AND default_time_to_live = 0
    AND gc_grace_seconds = 864000
    AND max_index_interval = 2048
    AND memtable_flush_period_in_ms = 0
    AND min_index_interval = 128
    AND read_repair_chance = 0.0
    AND speculative_retry = '99PERCENTILE';


```

#### Create a table in the keyspace

```cql
 CREATE TABLE employee_by_car_make_sorted
                 ... (car_make text,
                 ... age int,
                 ... id int,
                 ... name text,
                 ... PRIMARY KEY(car_make, age, id)); # car_make is the partition key and
```

#### Create a table consisting of two partitioning key and one clustering key

```cql
CREATE TABLE employee_by_car_make_and_model
                 ... (car_make text,
                 ... car_model text,
                 ... id int,
                 ... name text,
                 ... PRIMARY KEY((car_make, car_model), id));
```

```cql
cqlsh> CONSISTENCY
Current consistency level is ONE.
cqlsh> CONSISTENCY QUORUM;
Consistency level set to QUORUM.
cqlsh> CONSISTENCY ONE;
Consistency level set to ONE.
```

### Insert data into table

```cql
 INSERT INTO employee_by_id (id, name, position)
                 ... VALUES (1, 'John', 'Manager');
```

### select query

```cql
 SELECT * FROM employee_by_id;

 SELECT * FROM employee_by_id WHERE id = 1;

 SELECT * FROM employee_by_id WHERE name = 'John'; # InvalidRequest: Should query by id
```

# more insertion into table

```cql
cqlsh:test_keyspace> INSERT INTO employee_by_car_make(car_make, id, car_model)
                 ... VALUES ('BMW', 1, 'Sports Car');
cqlsh:test_keyspace> INSERT INTO employee_by_car_make(car_make, id, car_model) VALUES ('BMW', 2, 'Sports Car');
cqlsh:test_keyspace> INSERT INTO employee_by_car_make(car_make, id, car_model) VALUES ('AUDI', 4, 'Truck');
cqlsh:test_keyspace> INSERT INTO employee_by_car_make(car_make, id, car_model) VALUES ('AUDI', 5, 'Hatchback');
cqlsh:test_keyspace> SELECT * FROM employee_by_car_make;

 car_make | id | car_model
----------+----+------------
      BMW |  1 | Sports Car
      BMW |  2 | Sports Car
     AUDI |  4 |      Truck
     AUDI |  5 |  Hatchback

(4 rows)
```

##### More queries

```cql
cqlsh:test_keyspace> SELECT * FROM employee_by_car_make WHERE car_make='BMW';

 car_make | id | car_model
----------+----+------------
      BMW |  1 | Sports Car
      BMW |  2 | Sports Car

(2 rows)
cqlsh:test_keyspace> SELECT * FROM employee_by_car_make WHERE car_make='AUDI';

 car_make | id | car_model
----------+----+-----------
     AUDI |  4 |     Truck
     AUDI |  5 | Hatchback

(2 rows)
cqlsh:test_keyspace> SELECT * FROM employee_by_car_make WHERE id = 4;
InvalidRequest: Error from server: code=2200 [Invalid query] message="Cannot execute this query as it might involve data filtering and thus may have unpredictable performance. If you want to execute this query despite the performance unpredictability, use ALLOW FILTERING"
```

# more insertion into table

```cql
DESCRIBE TABLE employee_by_car_make_and_model;

INSERT INTO employee_by_car_make_and_model
                 ... (car_make, car_model, id, name)
                 ... VALUES ('BMW', 'Hatchback', 1, 'John')
                 ... ;
```

##### add more rows into the table

```cql
SELECT * FROM employee_by_car_make_and_model;

 car_make | car_model  | id | name
----------+------------+----+------
      BMW |  Hatchback |  1 | John
      BMW |  Hatchback |  2 | null
     AUDI |      Truck |  1 |  Amy
     AUDI |      Truck |  3 | null
     AUDI | Sports Car |  1 |  Tim

(5 rows)

```

#### Insertion using the same partition and clustering key updates the record

```cql

INSERT INTO employee_by_car_make_and_model (car_make, car_model, id, name) VALUES ('AUDI', 'Truck', 1, 'Neo') ;

SELECT * from employee_by_car_make_and_model;

 car_make | car_model  | id | name
----------+------------+----+------
      BMW |  Hatchback |  1 | John
      BMW |  Hatchback |  2 | null
     AUDI |      Truck |  1 |  Neo
     AUDI |      Truck |  3 | null
     AUDI | Sports Car |  1 |  Tim

(5 rows)
```

### perform query:

```cql
SELECT * from employee_by_car_make_and_model WHERE car_make = 'BMW' AND car_model = 'Hatchback';

 car_make | car_model | id | name
----------+-----------+----+------
      BMW | Hatchback |  1 | John
      BMW | Hatchback |  2 | null

(2 rows)

```

### Check the write times of the records

```cql
SELECT * FROM employee_by_car_make;

 car_make | id | car_model
----------+----+------------
      BMW |  1 | Sports Car
      BMW |  2 | Sports Car
     AUDI |  4 |      Truck
     AUDI |  5 |  Hatchback

(4 rows)

 SELECT car_make, car_model, writetime(car_model) FROM employee_by_car_make;

 car_make | car_model  | writetime(car_model)
----------+------------+----------------------
      BMW | Sports Car |     1575091410620740
      BMW | Sports Car |     1575091445314701
     AUDI |      Truck |     1575091464259521
     AUDI |  Hatchback |     1575091480107662

(4 rows)

```

##### drop table

```cql
DROP TABLE <table_name>;
```

##### drop keyspace

```cql
DROP KEYSPACE <keyspace_name>;
```

### Update record in table

```cql

cqlsh:test_keyspace> SELECT * FROM employee_by_car_make;

 car_make | id | car_model
----------+----+------------
      BMW |  1 | Sports Car
      BMW |  2 | Sports Car
     AUDI |  4 |      Truck
     AUDI |  5 |  Hatchback

(4 rows)

cqlsh:test_keyspace> UPDATE employee_by_car_make SET car_model='Truck' WHERE car_make = 'BMW' AND id = 2;
cqlsh:test_keyspace> SELECT * FROM employee_by_car_make;

 car_make | id | car_model
----------+----+------------
      BMW |  1 | Sports Car
      BMW |  2 |      Truck
     AUDI |  4 |      Truck
     AUDI |  5 |  Hatchback

(4 rows)

```

### Usage of TTL

```cql
cqlsh:test_keyspace> SELECT * FROM employee_by_car_make;

 car_make | id | car_model
----------+----+------------
      BMW |  1 | Sports Car
      BMW |  2 |      Truck
     AUDI |  4 |      Truck
     AUDI |  5 |  Hatchback

(4 rows)

cqlsh:test_keyspace> UPDATE employee_by_car_make USING TTL 60 SET car_model = 'Truck' WHERE car_make = 'BMW' and id = 1;
cqlsh:test_keyspace> SELECT * FROM employee_by_car_make;
 car_make | id | car_model
----------+----+-----------
      BMW |  1 |     Truck
      BMW |  2 |     Truck
     AUDI |  4 |     Truck
     AUDI |  5 | Hatchback

(4 rows)
```

After 60 seconds

```cql
cqlsh:test_keyspace> SELECT * FROM employee_by_car_make;
 car_make | id | car_model
----------+----+-----------
      BMW |  1 |     null
      BMW |  2 |     Truck
     AUDI |  4 |     Truck
     AUDI |  5 | Hatchback

(4 rows)
```

allow filtering
set
UUIDs
Counters

```cql
CREATE TABLE employee_by_uuid
(id uuid PRIMAY KEY, first_name text, last_name text);

INSERT INTO employee_by_uuid(id, first_name, last_name) values (uuid(), 'tom', 'dunne');

INSERT INTO employee_by_uuid(id, first_name, last_name) VALUES (uuid(), 'bob', 'hanson');
```

### timeuuid

```cql
CREATE TABLE employee_by_timeuuid
(id timeuuid PRIMAY KEY, first_name text, last_name text);

INSERT INTO employee_by_uuid(id, first_name, last_name) values (now(), 'tom', 'dunne');

INSERT INTO employee_by_uuid(id, first_name, last_name) VALUES (now(), 'bob', 'hanson');
```

### counters change only in increment/decrement

```cql

CREATE TABLE purchase_by_customer_id (id uuid PRIMARY KEY, purchases counter);

UPDATE purchase_by_customer_id SET purchases = purchases + 1 WHERE id = uuid();

SELECT * FROM purchases_by_customer_id;

UPDATE purchase_by_customer_id SET purchases = purchases + 1 WHERE id = "...";

```

### Materialized views

#### solves issues with secondary indexes

```cql
SELECT * FROM employee_by_car_make;

CREATE MATERIALIZED VIEW test_keyspace.employee_by_department
... AS SELECT *
... FROM test_keyspace.employee_by_car_make
... WHERE department IS NOT NULL AND
... car_make IS NOT NULL AND
... car_model IS NOT NULL AND
... id IS NOT NULL
... PRIMARY KEY (department, car_make, car_model, id);

SELECT * FROM employee_by_department;
SELECT * FROM employee_by_department WHERE department = 'IT';

UPDATE employee_by_car_make SET department='IT' WHERE car_make = 'AUDI' AND car_model='Saloon' AND id=2;
```

## materialized view also updates with base table


## Hue의 Offline 환경 구성을 위한 가이드 (Mac OS M1 기준)

#### 1. Docker image build

```shell script
# docker build . --platform amd64 -t hue:4.10.0.amd64 -f tools/docker/hue/Dockerfile
```

#### 2. Docker image save

```shell script
# docker save hue:4.10.0.amd64 -o hue-4.10.0.amd64.image
```

#### 3. Docker image load (설치할 대상 서버)

```shell script
# docker load -i hue-4.10.0.amd64.image
```

#### 4. Docker compose file 작성

```shell script
# vim docker-compose.yml
```

```shell script
version: '3'
services:
  hue-server:
    image: hue:4.10.0.amd64
    hostname: hue
    network_mode: host
    container_name: hue-4.10.0
    ports:
      - "8888:8888"
    volumes:
      - ./conf/hue.ini:/usr/share/hue/desktop/conf/z-hue-z.ini
```

#### 5. Hue configuration file 작성

```shell script
# vim conf/hue.ini
```

```shell script
[desktop]

# Hue Metadata Database
[[database]]
# IP address instead of FQDN
host=123.12.1.123
port=3306
engine=mysql
user=hue
password=passwordofhue
name=hue

# Set this to a random string, the longer the better.
secret_key=fhfhqoeifhqalsp138308hryryrq

# Webserver listens on this address and port
http_host=0.0.0.0
http_port=8888

[hadoop]

[[hdfs_clusters]]

[[[default]]]
fs.defaultfs=hdfs://123.12.1.123:8020
webhdfs_url=http://123.12.1.123:50071/webhdfs/v1

[[yarn_clusters]]

[[[default]]]
resourcemanager_host=master02
resourcemanager_api_url=http://master02:8088/
proxy_api_url=http://master02:8088/
resourcemanager_port=8032
history_server_api_url=http://master01:19888/

# Zookeeper Quorum을 통한 H/A HiveServer2 접속 정보
[beeswax]
hive_discovery_llap=true
hive_discovery_llap_ha=true

[libzookeeper]
ensemble=zk01:2181,zk02:2181,zk03:2181

[hbase]
hbase_clusters=(Cluster|master01:9090)
```

---

아래는 기존 README.md 내용이다.

---


[![CircleCI](https://img.shields.io/circleci/build/github/cloudera/hue/master.svg)](https://circleci.com/gh/cloudera/hue/tree/master)
[![DockerPulls](https://img.shields.io/docker/pulls/gethue/hue.svg)](https://registry.hub.docker.com/u/gethue/hue/)
![GitHub contributors](https://img.shields.io/github/contributors-anon/cloudera/hue.svg)

![Hue Logo](https://raw.githubusercontent.com/cloudera/hue/master/docs/images/hue_logo.png)


Query. Explore. Share.
----------------------

Hue is a mature SQL Assistant for querying any [Databases & Data Warehouses](https://docs.gethue.com/administrator/configuration/connectors/) via the Web:

Many companies and organizations use Hue to quickly answer questions via self-service querying e.g.:

* 1000+ customers
* Top Fortune 500

are executing 100s of 1000s of queries daily via its SQL Editor or API.

This open source project is also ideal for building your own [Query Editor](https://docs.gethue.com/developer/components/) or [Query Service](https://docs.gethue.com/administrator/installation/cloud/#kubernetes) and contributions are welcome. Read more on [gethue.com](http://gethue.com).

![Hue Editor](https://cdn.gethue.com/uploads/2021/02/hue-4.9.png)

Getting Started
---------------

You can start the server via three ways described below. Once setup, you would then need to configure the [desired databases](https://docs.gethue.com/administrator/configuration/connectors/) you want to query.

Quick Demos:

* Docker Compose: [Impala](https://gethue.com/blog/quickstart-sql-editor-for-apache-impala/), [Flink SQL](https://gethue.com/blog/sql-querying-live-kafka-logs-and-sending-live-updates-with-flink-sql/), [ksqlDB](https://gethue.com/blog/tutorial-query-live-data-stream-with-kafka-sql/), [Phoenix SQL / HBase](https://gethue.com/blog/querying-live-kafka-data-in-apache-hbase-with-phoenix/), [Spark SQL](https://gethue.com/blog/querying-spark-sql-with-spark-thrift-server-and-hue-editor/)
* Live instance: [demo.gethue.com](https://demo.gethue.com/)

Docker
------
Start Hue in a single click with the [Docker Guide](https://github.com/cloudera/hue/tree/master/tools/docker/hue) or the
[video blog post](http://gethue.com/getting-started-with-hue-in-2-minutes-with-docker/).

    docker run -it -p 8888:8888 gethue/hue:latest

Now Hue should be up and running on your default Docker IP on [http://localhost:8888](http://localhost:8888)!

Kubernetes
----------

    helm repo add gethue https://helm.gethue.com
    helm repo update
    helm install hue gethue/hue

Read more about configurations at [tools/kubernetes](tools/kubernetes/).

Development
-----------

For a very Quick Start go with the [Dev Environment Docker](https://docs.gethue.com/developer/development/#dev-docker).

Or install the [dependencies](https://docs.gethue.com/administrator/installation/dependencies/), clone the repository, build and get the server running.

    # <install OS dependencies>
    git clone https://github.com/cloudera/hue.git
    cd hue
    make apps
    build/env/bin/hue runserver

Now Hue should be running on [http://localhost:8000](http://localhost:8000)!

Read more in the [development documentation](https://docs.gethue.com/developer/development/).

Components
----------

Sql Editor and Sql Parsers [components](https://docs.gethue.com/developer/components/) and REST/Python [APIs](https://docs.gethue.com/developer/api/).


License
-----------
[Apache License, Version 2.0](http://www.apache.org/licenses/LICENSE-2.0)

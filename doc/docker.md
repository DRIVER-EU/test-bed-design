# Annex 4. Docker example for starting a Test-bed

A functional Test-bed requires many services, and installing them physically on a machine would require a substantial amount of time, and is error prone. Additionally, not every Trial or exercise requires the same setup, and therefore the Test-bed uses the virtualisation services provided by the open [Docker platform](https://www.docker.com).

To execute a successful Trial, you just need to combine the services that you need in a simple text file, often called `docker-compose.yml`, after which you can start all Test-bed services with a single command.

```bash
# For starting all services:
> docker-compose up -d
# Or to stop it and clean up everything, run:
> docker-compose down
# It removes everything so you can start with a clean slate,
# except for the recorded messages, which use a so-called 'volume.
```

A minimal example is given below, but actually used versions during the Trials can be found at the [Test-bed website at https://github.io/DRIVER-EU/test-bed](https://github.com/DRIVER-EU/test-bed/tree/master/docker). In the example, besides Apache Kafka for sharing information, and Apache Zookeeper for keeping track of client sessions, you see the schema registry for registering all used schemas, a time service, and the Trial Management Tool. The large file service is commented out (not needed at the moment). Typical Test-beds are more extensive, and contain additional tools such as the admin tool, AAR, OST, schema registry UI, and several more.

```yaml
---
version: '3'
services:
  zookeeper:
    image: confluentinc/cp-zookeeper:5.0.0
    hostname: zookeeper
    ports:
      - "3500:3500"
    environment:
      ZOOKEEPER_CLIENT_PORT: 3500
      ZOOKEEPER_TICK_TIME: 2000

  broker:
    image: confluentinc/cp-kafka:5.0.0
    hostname: broker
    depends_on:
      - zookeeper
    ports:
      - "3501:3501"
    environment:
      KAFKA_BROKER_ID: 1
      KAFKA_ZOOKEEPER_CONNECT: 'zookeeper:3500'
      KAFKA_ADVERTISED_LISTENERS: 'EXTERNAL://localhost:3501,PLAINTEXT://broker:9092'
      KAFKA_LISTENER_SECURITY_PROTOCOL_MAP: 'EXTERNAL:PLAINTEXT,PLAINTEXT:PLAINTEXT'
      KAFKA_LISTENERS: 'EXTERNAL://0.0.0.0:3501,PLAINTEXT://0.0.0.0:9092'
      KAFKA_OFFSETS_TOPIC_REPLICATION_FACTOR: 1
      KAFKA_DEFAULT_REPLICATION_FACTOR: 1
      KAFKA_MESSAGE_MAX_BYTES: 100000000
      KAFKA_REPLICA_FETCH_MAX_BYTES: 100000000

  schema_registry:
    image: confluentinc/cp-schema-registry:5.0.0
    hostname: schema_registry
    depends_on:
      - zookeeper
      - broker
    ports:
      - "3502:3502"
    environment:
      SCHEMA_REGISTRY_HOST_NAME: schema_registry
      SCHEMA_REGISTRY_LISTENERS: 'http://0.0.0.0:3502'
      SCHEMA_REGISTRY_KAFKASTORE_CONNECTION_URL: 'zookeeper:3500'
      SCHEMA_REGISTRY_KAFKASTORE_BOOTSTRAP_SERVERS: 'PLAINTEXT://broker:9092'
  
  time_service:
    image: drivereu/test-bed-time-service:latest
    depends_on:
      - broker
      - schema_registry
    ports:
      - "8100:8100"
    environment:
      KAFKA_BROKER_URL: broker:9092
      SCHEMA_REGISTRY_URL: http://schema_registry:3502
      AUTO_REGISTER_SCHEMAS: 'true'

  # large_file_service:
  #   image: drivereu/large-file-service:latest
  #   ports:
  #     - '9090:9090'
  #   environment:
  #     HOST: localhost
  #     PORT: 9090
  
  trial_management_tool:
    image: drivereu/trial-management-tool:latest
    depends_on:
      - broker
      - schema_registry
    ports:
      - '3210:3210'
    environment:
      CLIENT_ID: TB-TrialMgmt
      KAFKA_HOST: broker:9092
      SCHEMA_REGISTRY: http://schema_registry:3502
      TRIAL_MANAGER_SERVER_PORT: 3210
      PRODUCE: system_request_change_of_trial_stage,system_tm_phase_message,system_tm_role_player,system_tm_session_mgmt
      SSL: 'false'
      SSL_PFX: certs/TB-TrialMgmt.p12
      SSL_PASSPHRASE: changeit
      SSL_CA: certs/test-ca.pem
    volumes:
      - trial-data:/app/trials

volumes:
  trial-data:
```

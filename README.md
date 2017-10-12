# kafka-connect-splunk
This is a Kafka Connect Sink for Splunk.
SplunkSinkConnector supports writing data from Kafka topics into Splunk via Splunk HTTP Event Collector.
 
 
## Build
1. Clone the https://github.com/splunk/kafka-connect-splunk
2. Make sure Java8 JRE or JDK is installed
3. Run `bash build.sh`
 
 
## Run Kafka Connect
1. [Start up](https://kafka.apache.org/quickstart) Kafka Cluster and Zookeeper locally and create a test topic (eg: `perf`). If you have already had a cluster, then use the existing cluster is fine.
2. Inject events to `perf` topic for example using [Kafka data-gen-app](https://github.com/dtregonning/kafka-data-gen) or use kafka-console-consumer
3. Untar the package, `tar xzvf kafka-connect-splunk.tar.gz`
4. Enter `kafka-connect-splunk`
5. Adjust `bootstrap.servers` and `plugin.path` in `config/connect-distributed.properties` for your environment if necessary. Default values should be fine for experiment
6. Run `./bin/connect-distributed.sh config/connect-distributed.properties` to start Kafka Connect
7. Run the following command to create a connector tasks. Adjust the `topics`, `tasks.max`, `indexes`, `sources`, `sourcetypes` and `hec` etc settings if necessary
 
    ```
    curl localhost:8083/connectors -X POST -H "Content-Type: application/json" -d '{
    "name": "kafka-connect-splunk",
    "config": {
       "topics": "<list-of-topics-separated-by-comma>",
       "indexes": "<list-of-indexes-for-topics-data-separated-by-comma>",
       "sources": "<list-of-sources-for-topics-data-separated-by-comma>",
       "sourcetypes": "<list-of-sourcetypes-for-topics-data-separated-by-comma>",
       "tasks.max": "3",
       "connector.class": "com.splunk.kafka.connect.SplunkSinkConnector",
       "splunk.hec.uri": "<Splunk-HEC-URI>",
       "splunk.hec.token": "<Splunk-HEC-Token>",
       "splunk.hec.raw": "false",
       "splunk.hec.ack.enabled": "true",
       "splunk.hec.ssl.validate.certs": "false",
       "name": "kafka-connect-splunk"
      }
    }'
       
    ```
8. If everything is fine, data should flow to splunk
9. Run the following commands to check the status of connector and tasks
  
    ```
    # List active connectors
    curl http://localhost:8083/connectors
     
    # Get kafka-connect-splunk connector info
    curl http://localhost:8083/connectors/kafka-connect-splunk
     
    # Get kafka-connect-splunk connector config info
    curl http://localhost:8083/connectors/kafka-connect-splunk/config
 
    # Validate kafka-connect-splunk connector config
    curl http://localhost:8083/connectors/kafka-connect-splunk -X DELETE
     
    # Get kafka-connect-splunk connector task info
    curl http://localhost:8083/connectors/kafka-connect-splunk/tasks
     
    # Delete kafka-connect-splunk connector
    curl http://localhost:8083/connectors/kafka-connect-splunk -X DELETE
 
    Refer to [this](https://docs.confluent.io/current/connect/managing.html#common-rest-examples) for more examples.
    ```
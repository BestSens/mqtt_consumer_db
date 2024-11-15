# MQTT Consumer DataBase External Input Plugin

This plugin updates the subscription topics of the mapped 'mqtt_consumer' plugin 
using a database connection. By accessing the read-only account of the underlying 
MQTT broker, it adjusts the topics whenever the subscriptions are updated.

## Configuration

```toml @sample.conf
[[inputs.mqtt_consumer_db]]
  db_server = "http://localhost:8086"
  db_name = "telegraf"
  db_username = "telegraf"
  db_password = "telegraf"
  server_id = "server1"
  
  name_override = "dot"
  
  tags = {dot = "default_event_log"}
  
  data_format = "json_v2"
  [inputs.mqtt_consumer_db.json_v2]
    [[inputs.mqtt_consumer_db.json_v2.json_v2]]
	  measurement_name = "dot"
      [[inputs.mqtt_consumer_db.json_v2.json_v2.object]]
        path = "@this"
		optional = false
        timestamp_key = "ts"
        timestamp_format = "unix_ms"
		timestamp_timezone = "UTC"
        excluded_keys = []
		included_keys = []
[inputs.mqtt_consumer_db.mqtt_consumer]
  ## Broker URLs for the MQTT server or cluster.  To connect to multiple
  ## clusters or standalone servers, use a separate plugin instance.
  ##   example: servers = ["tcp://localhost:1883"]
  ##            servers = ["ssl://localhost:1883"]
  ##            servers = ["ws://localhost:1883"]
  servers = ["tcp://127.0.0.1:1883"]

  ## Topics that will be subscribed to.
  ## Will be overwritten by mqtt_consumer_db
  ##topics = [
  ##  "telegraf/host01/cpu",
  ##  "telegraf/+/mem",
  ##  "sensors/#",
  ##]

  ## The message topic will be stored in a tag specified by this value.  If set
  ## to the empty string no topic tag will be created.
  # topic_tag = "topic"

  ## QoS policy for messages
  ##   0 = at most once
  ##   1 = at least once
  ##   2 = exactly once
  ##
  ## When using a QoS of 1 or 2, you should enable persistent_session to allow
  ## resuming unacknowledged messages.
  # qos = 0

  ## Connection timeout for initial connection in seconds
  connection_timeout = "30s"

  ## Max undelivered messages
  ## This plugin uses tracking metrics, which ensure messages are read to
  ## outputs before acknowledging them to the original broker to ensure data
  ## is not lost. This option sets the maximum messages to read from the
  ## broker that have not been written by an output.
  ##
  ## This value needs to be picked with awareness of the agent's
  ## metric_batch_size value as well. Setting max undelivered messages too high
  ## can result in a constant stream of data batches to the output. While
  ## setting it too low may never flush the broker's messages.
  # max_undelivered_messages = 1000

  ## Persistent session disables clearing of the client session on connection.
  ## In order for this option to work you must also set client_id to identify
  ## the client.  To receive messages that arrived while the client is offline,
  ## also set the qos option to 1 or 2 and don't forget to also set the QoS when
  ## publishing. Finally, using a persistent session will use the initial
  ## connection topics and not subscribe to any new topics even after
  ## reconnecting or restarting without a change in client ID.
  # persistent_session = false

  ## If unset, a random client ID will be generated.
  # client_id = ""

  ## Username and password to connect MQTT server.
  # username = "telegraf"
  # password = "metricsmetricsmetricsmetrics"

  ## Optional TLS Config
  # tls_ca = "/etc/telegraf/ca.pem"
  # tls_cert = "/etc/telegraf/cert.pem"
  # tls_key = "/etc/telegraf/key.pem"
  ## Use TLS but skip chain & host verification
  # insecure_skip_verify = false

  ## Client trace messages
  ## When set to true, and debug mode enabled in the agent settings, the MQTT
  ## client's messages are included in telegraf logs. These messages are very
  ## noisey, but essential for debugging issues.
  # client_trace = false

  ## Enable extracting tag values from MQTT topics
  ## _ denotes an ignored entry in the topic path
  # [[inputs.mqtt_consumer.topic_parsing]]
  #   topic = ""
  #   measurement = ""
  #   tags = ""
  #   fields = ""
  ## Value supported is int, float, unit
  #   [inputs.mqtt_consumer.topic_parsing.types]
  #      key = type
```

## MQTT Consumer configuration

See [README.md][mqtt_consumer] of the mqtt_consumer plugin.


[mqtt_consumer]: /plugins/inputs/mqtt_consumer/README.md
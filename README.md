# bwNetFlow - Open Source Network Flow Analysis Suite

![bwNetFlow Overview](overview.png "bwNetFlow Overview")

The bwNetFlow suite uses existing software and provides
glue codes to allow for large scale network flow analysis.

bwNetFlow uses [GoFlow](https://github.com/cloudflare/goflow) and
[Apache Kafka](https://kafka.apache.org/) to process network flow via NetFlow and Kafka.

We provide a set of tools working with Kafka as consumers and/or producers
to establish a flow monitoring analysis pipeline. While these tools can be combined 
in any specific way, the core components and wiring in our use case is as follows:

- **GoFlow:** Cloudflare's [GoFlow](https://github.com/cloudflare/goflow) receives NetFlow and produces protobuf messages in Kafka topic *input*
- **Enricher:** reads from Kafka topic *input*, adds domain specific knowlege (customer, direction, device info, etc), and writes protobuf messages in Kafka topic *enriched*
- **Splitter:** reads from Kafka topic *enriched*, writes into customer specific topics *enriched-$cid* for each enabled customer
- **Dashboard:** reads from Kafka topic *enriched*, aggregates the flows to counters and writes these counter values to a Time Series Database (InfluxDB or Prometheus)

The tools work with Protobuf messages for representing NetFlow packets from
GoFlow - yet with an [extended protobuf message](https://github.com/bwNetFlow/protobuf) as soon as enriched by the enricher component. 

Other Tools:

- **consumers_example:** tbd
- **consumer_dumper:** tbd
- **consumer_counter:** tbd
- **outliers-detection:** tbd
- **protobuf_to_netflow_converter:** tbd
- **processor_reducer:** tbd

To develop Kafka consumers/producers with Go the [kafkaconnector](https://github.com/bwNetFlow/kafkaconnector) library abstracts most of the recurrent code fragments.

## Deployment

For deploying the bwNetFlow suite we provide Ansible scripts or Docker / Docker-Compose description.

tbd
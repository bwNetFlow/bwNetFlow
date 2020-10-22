# bwNetFlow

The bwNetFlow platform uses existing software and provides glue code to allow
for large scale network flow analysis. BwNetFlow mainly uses [goflow][goflow]
and [Apache Kafka][kafka] to process network flows in the form of
[protobuf][protobuf] messages.

## Overview

We provide a set of tools working with Kafka as consumers, producers, or both
(we call those processors) to establish a flow monitoring analysis pipeline.
While these tools can be combined in any specific way, the core components and
wiring in our use case is as follows:

- **Goflow:** Cloudflare's [goflow][goflow] receives NetFlow v9 from our
  external border interfaces and produces protobuf messages in a Kafka topic.
- **Enricher:** Our [enricher][enricher] reads from this topic and adds domain
  specific knowlege (customer ID, device info, geolocation, etc.) during
  transit into another Kafka topic.
- **Splitter:** Our [splitter][splitter] reads the enriched topic and splits
  the stream of flow messages into customer-specific topics, enabling
  multi-tenancy through Kafkas authentication and access control system.
- **Reducer:** Our [reducer][reducer] has the capability to reduce any topic's
  flows to some subset of its fields. It also has some anonymization routines.
- **Dashboard:** Our [dashboard application][dashboard] reads from the
  un-splitted, enriched topic. It then aggregates the flows to counters and
  writes these counter values to a Time Series Database (InfluxDB or
  Prometheus). This could be run on customer topics too, but InfluxDB (which we
  use with this programm) can handle multiple customers through its
  authentication features.

These components of our platform have been built using our
[kafkaconnector][kafkaconnector] library, which abstracts most of the recurrent
code fragments and already makes resonable assumptions based on what we are doing.

## Customers and Users

In our setup, customers interact with the bwNetFlow platform in one of two ways:

1. they look at the dashboards we generate using Grafana, InfluxDB, and the
   aforementioned [dashboard application][dashboard]
2. they connect to Kafka themselves, using some custom tool written by them in
   a language of their choosing, given it supports Kafka and Protobuf. Some
   examples directly pulled from [BelWü][belwue]'s IP department can be found
   [here][python-consumers]. They include fancy printing, `htop`-style CLI
   viewers, flow field searches, bogon detection, and even Sqlite dumping of
   flows.

## Additional Tools

- **Protobuf to Netflow v9 Converter:** Our
  [protobuf_to_netflow_converter][pb2nf] converts all incoming protobuf decoded
  messages into NetFlow v9 compliant messages, which is useful for customers
  with existing Netflow-consuming setups.
- **Protobuf to CSV converter:** The [protobuf_to_csv_converter][pb2csv] writes
  all incoming protobuf decoded messages into csv files. The specific protobuf
  fields can be specified by the user.
- **DDoS detection:** [This][ddos] provides a proof-of-concept volume-based DoS
  Detection implementation based on protobuf converted NetFlow data.
- **NetFlow NEMEA Toolchain:** [This][nemea] an example how the bwNetFlow
  NetFlow v9 exporter and the NEMEA framework can be chained. This can be
  applied similarly to all other available NEMEA modules.

To develop Kafka consumers/producers with C++ as these additional tools mostly
are, our [cpp_kafkaconnector](https://github.com/bwNetFlow/cpp_kafkaconnector)
library abstracts most of the recurrent code fragments and makes reasonable
assumptions for our use case.

## Deployment

Our main components are available from their respective repositories and also
as container images (currently only via ghcr.io, see the *Packages* tab on our
organization page). As for container deployments, have a look at the
[demo][demo] deployment for our customers (~~or our [fullstack
docker-composable][docker-composable]~~ coming soon...).

Additionally, we're working to release our Ansible playbooks used for our
currently largest deployment.

## Media
See our presentation from SC19's INDIS workshop
[here](media/sc19-S1P2_ws_indis108s2_Nagele.pdf), or get the executive summary
from our poster below.

![bwNetFlow Poster](media/poster.png "bwNetFlow Poster")

[goflow]: https://github.com/cloudflare/goflow
[kafka]: https://kafka.apache.org/
[protobuf]: https://github.com/bwNetFlow/protobuf
[enricher]: https://github.com/bwNetFlow/processor_enricher
[splitter]: https://github.com/bwNetFlow/processor_splitter
[reducer]: https://github.com/bwNetFlow/processor_reducer
[dashboard]: https://github.com/bwNetFlow/dashboard
[kafkaconnector]: https://github.com/bwNetFlow/kafkaconnector
[belwue]: https://www.belwue.de/
[python-consumers]: https://github.com/bwNetFlow/python-consumers
[demo]: https://github.com/bwNetFlow/demo
[fullstack-composable]: https://github.com/bwNetFlow/fullstack-composable
[pb2nf]: https://github.com/bwNetFlow/protobuf_to_netflow_converter
[pb2csv]: https://github.com/bwNetFlow/protobuf_to_csv_converter
[ddos]: https://github.com/bwNetFlow/bwnetflow_dosdetection
[nemea]: https://github.com/bwNetFlow/NetFlow_NEMEA_Toolchain

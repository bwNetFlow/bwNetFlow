# bwNetFlow - Open Source Network Flow Analysis Suite

The bwNetFlow suite uses existing software and provides glue codes to allow for large scale network flow analysis.

```
                                                  +---------------+
                                                  |               |
                                                  |  Kafka        |
                                                  |  Cluster      |
+-------------+                                   |               |          CONSUMERS/
|             |            +------------+         |  Topics:      |           PRODUCERS:
|             |            |            |         |               |
|  NetFlow    | +------->  |   GoFlow   | +--------> input     +---------->  ENRICHER
|  Exporters  |            |            |         |            <----------+
|             |            +------------+         |  enriched     |
|             |                                   |            +---------->  SPLITTER
+-------------+                                   |  per-user  <----------+
                                                  |               |
 e.g. Routers,                                    |            +---------->  DASHBOARD
 Hypervisors, etc.                                |            <----------+
                                                  +---------------+             +
                                                                                |
                                                                                v

                                                                             Prometheus

                                                                              Grafana
```

The components from left to right:
 - **NetFlow Exporters** software or hardware which samples network traffic and sends NetFlow packets to a collector. 
   - [flowexport](https://github.com/cha87de/flowexport) allows to export flows from a linux server
 - **GoFlow** serves as NetFlow collector and writes the flow packets to a Kafka cluster into the `input` topic
 - **Kafka Cluster** is the central communication point
 - **Consumers/Producers** 
   - **Enricher** interprets the raw input network flows and adds additional information (e.g. Customer IDs)
   - **Splitter** splits the enriched network flows into isolated topics for each customer
   - **Dashboard** aggregates the flows into counter values and exports them for a Prometheus time series database

# Usage Guide

tbd


# Development Guide

tbd
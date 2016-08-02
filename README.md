# JvsionDecoderToInfluxdb
Jvision OpenConfig decoder to InfluxDB writer Interfaced with Grafana as Analytics Collector

## Getting Started
This project is a telemetry service made for real time metrics analysis of Jvision-QFX servers. These servers run a GRPC(General Remote Procedure Calling)-server program that collects metrics and forwards to its subscribed GRPC-clients.

GRPC-server are configured to communicate by a global standard protocol called GPB(Google protocol buffer) that defines communication in OpenConfig format.

Know more about OpenConfig: http://www.openconfig.net/

Know more about GBP:        https://developers.google.com/protocol-buffers/

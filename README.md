# JvsionDecoderToInfluxdb
Jvision OpenConfig decoder to InfluxDB writer Interfaced with Grafana as Analytics Collector

## Getting Started
This project is a telemetry service made for real time metrics analysis of Jvision-QFX servers. These servers run a GRPC(General Remote Procedure Calling)-server program that collects metrics and forwards to its subscribed GRPC-clients.

GRPC-server are configured to communicate by a global standard protocol called GPB(Google protocol buffer) that defines communication in OpenConfig format.

Know more about OpenConfig: http://www.openconfig.net/

Know more about GBP:        https://developers.google.com/protocol-buffers/

##JvisionDecoder
JvisionDecoder is a GRPC-client that collects streaming data from QFX-servers and serializes structured data. Writes the collected data into a log with port number being listened to.
Ex: jvision_port_2000.txt, jvision_port_3002.txt, jvision_port_9000.txt

#JvsionDecoderToInfluxdb.py
Inorder to visualize JvisionDecoder logs on a graph metrics. Grafana-InfluxDB framework is used. Grafana is a Metrics, Analytics, dashboards and monitoring tool, when this is used along with Influx Database(Time-series data storage) we can diplay a live monitoring tool for Jvision-QFX.

(More info: http://grafana.org/ https://influxdata.com/time-series-platform/influxdb/)

### Prerequisities
All commands are used on  DEB (Ubuntu / Debian 64bit) OS. If you are using a different system please goto Grafana and InfluxDB homepage and follow the instructions.

What things you need to install the software and how to install them

Grafana
```
$ wget https://grafanarel.s3.amazonaws.com/builds/grafana_3.1.1-1470047149_amd64.deb
$ sudo apt-get install -y adduser libfontconfig
$ sudo dpkg -i grafana_3.1.1-1470047149_amd64.deb
```

InfluxDB
```
wget https://dl.influxdata.com/influxdb/releases/influxdb_0.13.0_amd64.deb
sudo dpkg -i influxdb_0.13.0_amd64.deb
```


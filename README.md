# JvsionDecoderToInfluxdb
Jvision OpenConfig decoder to InfluxDB writer Interfaced with Grafana as Analytics Collector

## Getting Started
This project is a telemetry service made for real time metrics analysis of Jvision-QFX servers. These servers run a GRPC(General Remote Procedure Calling)-server program that collects metrics and forwards to its subscribed GRPC-clients.

GRPC-server are configured to communicate by a global standard protocol called GPB(Google protocol buffer) that defines communication in OpenConfig format.

Know more about OpenConfig: http://www.openconfig.net/

Know more about GPB:        https://developers.google.com/protocol-buffers/

##JvisionDecoder
JvisionDecoder is a GRPC-client that collects streaming data from QFX-servers and serializes structured data. Writes the collected data into a log with port number being listened to.
Ex: jvision_port_2000.txt, jvision_port_3002.txt, jvision_port_9000.txt

#JvsionDecoderToInfluxdb.py
Inorder to visualize JvisionDecoder logs on a graph metrics. Grafana-InfluxDB framework is used. Grafana is a Metrics, Analytics, dashboards and monitoring tool, when this is used along with Influx Database(Time-series data storage) we can diplay a live monitoring tool for Jvision-QFX.

This script writes Openconfig Jvision logs to InfluxDatabase serially. The script uses a follow code to check new changes to the log file and dump it into database, which gives user a real time analysis output. (The script is made to read one line above the current line to eliminate any incomplete log line)

(More info: http://grafana.org/ https://influxdata.com/time-series-platform/influxdb/)

### Installing
All commands are used on  DEB (Ubuntu / Debian 64bit) OS. If you are using a different system please goto Grafana and InfluxDB homepage and follow the instructions.

What things you need to install the software and how to install them

Python2.7 (Skip if already installed)

Grafana
```
$ wget https://grafanarel.s3.amazonaws.com/builds/grafana_3.1.1-1470047149_amd64.deb
$ sudo apt-get install -y adduser libfontconfig
$ sudo dpkg -i grafana_3.1.1-1470047149_amd64.deb
```

InfluxDB
```
$ wget https://dl.influxdata.com/influxdb/releases/influxdb_0.13.0_amd64.deb
$ sudo dpkg -i influxdb_0.13.0_amd64.deb
```

Kapacitor (Optional)
For Time-Series Data Processing, Alerting And Anomaly Detection install Kapacitor which can be interfaced with Slack Messenger app to get info, warnings and critical updates via channel stream broadcasting.

```
$ wget https://dl.influxdata.com/kapacitor/releases/kapacitor_0.13.1_amd64.deb
$ sudo dpkg -i kapacitor_0.13.1_amd64.deb
```

###Prerequisities
After Installing Grafana, InfluxDB and Kapacitor. Follow these steps to connect each of the application to form a framework.

1. Create a database in InfluxDB and call it "jvision"
```

```
2. Goto Grafana web interface on http://127.0.0.1:3000 or http://localhost:3000 and add a datasource linked to influxdb "jvision" on port 8086. i.e https:
Web interface for garafan: http://127.0.0.1:3000
Web interface for influxdb: http://127.0.0.1:8083

Communication to influxdb (reading and writing): http://127.0.0.1:8086

2. 

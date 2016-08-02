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

1. Start InfluxDB-server. Open a web browser and access web interface for InfluxDB. Create a database in InfluxDB and call it "jvision"
```
$ sudo service influxdb start
```
Web interface for influxdb: http://127.0.0.1:8083

Query: CREATE DATABASE "jvision"

2. Start Grafana-server. Login to Grafana: admin/admin. Goto Grafana web interface and add a datasource linked to influxdb "jvision" on port 8086. i.e https://127.0.0.1:8086 with database: jvision, User: root, Password: root

```
$ sudo service grafana-server start
```
Web interface for garafan: http://127.0.0.1:3000

Source: http://docs.grafana.org/datasources/influxdb/

Communication to influxdb (reading and writing): http://127.0.0.1:8086

3. (Optional) Start Kapacitor. and follow bellow instructions.
Run the following command to create a default configuration file:
```
kapacitord config > kapacitor.conf
```
Edit kapacitor.conf and uncomment slack configuation and add new incoming web hook

Send the alert to Slack. To allow Kapacitor to post to Slack, go to the URL https://slack.com/services/new/incoming-webhook and create a new incoming webhook and place the generated URL in the 'slack' configuration section. Example:

```
[slack]
  enabled = true
    url = "https://hooks.slack.com/services/xxxxxxxxx/xxxxxxxxx/xxxxxxxxxxxxxxxxxxxxxxxx"
    channel = "#general"
```

include jvision.tick script(source code in the repo) and run kapacitor with the config file.
```
kapacitord -config kapacitor.conf
```

Configure kapacitor with jvision.tick script: (jvision_now is kapacitor instance )
```
$ kapacitor define -name jvision_now -type stream -tick jvision.tick -dbrp jvision.awesome_policy
$ kapacitor enable jvision_now
$ kapacitor show jvision_now
```

Install Slack and login to the channel to receive messages.

https://slack.com/downloads


## Running the tests
"Required Jvision_decoder.py to be running and collecting data into log file".

Start srcipt JvisionDecoderToInfluxdb.py to follow log file and display on grafana.

Usage:
```
$ python jvision_to_influxreadable.py --help
usage: jvision_to_influxreadable.py [-h] [--file FILE]

Jvision to InfluxDB writer. Usage $python <jvision_to_influxreadable.py>
<jvision_log_file.txt>

optional arguments:
  -h, --help   show this help message and exit
  --file FILE  jvision decoder log txt_file.txt
```

Example running script:
```
root@jvision-lnx01:~/influx_writer# python jvision_to_influxreadable.py --file /root/jvision_collector/jvision_log/jvision_port_3006.txt
Writing /root/jvision_collector/jvision_log/jvision_port_3006.txt to InfluxDB
1469729626.56
2016-07-28T11:13:46.558000Z
True
1
---------------NEW INPUT-----------------
1469729627.56
2016-07-28T11:13:47.558000Z
True
2
---------------NEW INPUT-----------------
1469729628.56
2016-07-28T11:13:48.558000Z
True
3
---------------NEW INPUT-----------------
1469729629.56
2016-07-28T11:13:49.558000Z
True
4
---------------NEW INPUT-----------------
1469729630.56
2016-07-28T11:13:50.558000Z
True
5
---------------NEW INPUT-----------------
1469729631.56
2016-07-28T11:13:51.557000Z
```

###Grafana
Create graphs on Grafana to query into InfluxDB.

Set refresh time policy to '5s' and zoom into the required graph for better visualisation.
Add in multiple graphs and queries for multi-visualization.

###Kapacitor
Edit jvision.tick to set required boundaries to monitor parameters and alert when the conditions does not meet the normal operation of the server.

Update: Kapacitor uses TICK script. Use "lambda:" formulae to add watch algorithms to monitor and report depending on the algorithm.

TICK script lambda expressions: https://docs.influxdata.com/kapacitor/v0.13/tick/expr/

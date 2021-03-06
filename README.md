# JvsionDecoderToInfluxdb.py
In order to visualize JvisionDecoder logs on a graph metrics. The framework used is Grafana-InfluxDB. Grafana is a Metrics, Analytics, dashboards and monitoring tool, when this is used along with Influx Database(Time-series data storage) we can display a live monitoring tool for Jvision-QFX.

![Alt text](/images/4.jpg?raw=true "Framework")

JvsionDecoderToInfluxdb.py script writes Openconfig Jvision logs to InfluxDatabase serially. The script uses a follow code to check new changes to the log file and dumps it into database, which gives user a real time analysis output. (The script is made to read one line above the current line to eliminate any incomplete log line).

(More info: http://grafana.org/ https://influxdata.com/time-series-platform/influxdb/)

### Installing
All commands are used on  DEB (Ubuntu / Debian 64bit) OS. If you are using a different system, please go to Grafana and InfluxDB homepage and follow the instructions for specific environment.

What things you need to install the software and how to install them.

Python2.7 (Skip if already installed)
```
$ sudo apt-get install build-essential checkinstall
$ sudo apt-get install libreadline-gplv2-dev libncursesw5-dev libssl-dev libsqlite3-dev tk-dev libgdbm-dev libc6-dev libbz2-dev
```
Then download using the following command:
```
cd ~/Downloads/
wget http://python.org/ftp/python/2.7.5/Python-2.7.5.tgz
```
Extract and go to the dirctory:
```
tar -xvf Python-2.7.5.tgz
cd Python-2.7.5
```
Now, install using the command you just tried:
```
./configure
make
sudo checkinstall
```

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

For Time-Series Data Processing, Alerting and Anomaly Detection install Kapacitor which can be interfaced with Slack Messenger app to get info, warnings and critical updates via channel stream broadcasting.

```
$ wget https://dl.influxdata.com/kapacitor/releases/kapacitor_0.13.1_amd64.deb
$ sudo dpkg -i kapacitor_0.13.1_amd64.deb
```

### Prerequisite configurations
After Installing Grafana, InfluxDB and Kapacitor. Follow these steps to connect each of the application to form a framework.

1. Start InfluxDB-server. Open a web browser and access web interface for InfluxDB http://127.0.0.1:8083. Create a database in InfluxDB and call it "jvision". Query: CREATE DATABASE "jvision"
```
$ sudo service influxdb start
```

2. Start Grafana-server. Open a web browser and access web interface for InfluxDB http://127.0.0.1:3000. Login to Grafana: admin/admin. Goto Grafana web interface and add a datasource linked to influxdb "jvision" on port 8086. i.e https://127.0.0.1:8086 with database: jvision, User: root, Password: root
```
$ sudo service grafana-server start
```

3. (Optional) Start Kapacitor. and follow below instructions.
Run the following command to create a default configuration file:
```
kapacitord config > kapacitor.conf
```
Edit kapacitor.conf and uncomment slack configuration and add new incoming web hook.

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

Install Slack(https://slack.com/downloads) and login to the channel to receive messages.

## Running the script
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

### Grafana
Create graphs on Grafana to query into InfluxDB.

Set refresh time policy to '5s' and zoom into the required graph for better visualization.
Add in multiple graphs and queries for multi-visualization.

Adding graphs in Grafana: http://docs.grafana.org/guides/gettingstarted/

### InfluxDB
Goto InfluxDB web interface: https://127.0.0.1:8083. 

Select database jvision and Query:

```
SHOW MEASUREMENTS - Display list of servers
SELECT * FROM <MEASUREMENT> - Display all key-value pair of <MEASUREMENT> server
SHOW FIELD KEYS - Display only field keys
SHOW TAG KEYS - Display only tag keys
```

### Kapacitor
Edit jvision.tick to set required boundaries to monitor parameters and alert when the conditions does not meet the requirements. For example when the normal operation of the server is stalled or slows down below a normal operating range like. if tx_pps > 800000 raise critical message.

Note: Kapacitor uses TICK script. Use "lambda:" formulae to add watch algorithms to monitor and report info depending on the algorithm.

TICK script lambda expressions: https://docs.influxdata.com/kapacitor/v0.13/tick/expr/

## Built With
* Python2.7
* Grafana
* InfluxDB
* Kapacitor
* Slack Messenger

## Future improvements
* To implement the entire framework on a docker containers for easy portability.
* Using the big data analysis to come up with a performance tuning metrics using machine learning algorithm.

## Authors
* **Nishant Patil** - *Initial work*
* **Anju Gaur** - *Project Manager*
* **Ashok Kachana** - *Project Mentor*

## License
Juniper Networks Inc.

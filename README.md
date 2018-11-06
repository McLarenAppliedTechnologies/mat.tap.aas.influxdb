# ![logo](/docs/branding.bmp) Telemetry Analytics Data Persistence

![Build Status](https://mat-ocs.visualstudio.com/Telemetry%20Analytics%20Platform/_apis/build/status/MAT.TAP.TelemetryAnalytics.API/MAT.TAP.TelemetryAnalytics.API%20-%20Pull%20Request%20Gateway?branchName=develop)

Telemetry data and metadata about sessions are saved to various storage implementations such as SqlRace and InfluxDb and relational databases. This article discusses saving telemetry data to InfluxDb as time-series data and metadata to MSSQL Server.

## InfluxDb Writer

InfluxDb writer subscribes to message brokers and saves telemetry data and session metadata in real-time to time-series and relational databases respectively. InfluxDb Writer is platform-independent and hence can be deployed on Windows or Unix-based systems as a service but **we strongly recommend deploying InfluxDb writer and InfluxDb on one Linux VM. InfluxDb performs better on Linux and by deploying it on one machine together with service you reduce unnecessary network bandwidth between those services.**

#### Basic usage

In order to use InfluxDb writer, add the relevant configuration in `config.json` file and start service using

    dotnet MAT.TAP.AAS.InfluxDb.Writer.dll

A sample configuration and an explanation of settings is given below.

    {
        "BrokerList": "xx.xxx.x.xx",
        "DependencyUrl": "http://[hostname/ip_address]/api/dependencies/",
        "DependencyGroup": "[dependency group identifier]",
        "BatchSize": 100000,
        "ThreadCount": 5,
        "InitializeDatabase": true,
        "Connections": {
            "[TopicName]": {
            "InfluxConnections": {
                "*": {
                "InfluxDbUrl": "http://[hostname/ip_address]"
                }
            },
            "SqlServerConnectionString": "Server=(localdb)\\MSSQLLocalDB;Initial Catalog=[DatabaseName];User Id=[Username];Password=[Password];"
            }
        }
    }

- `BrokerList`: Address of the message broker cluster.
- `DependencyUrl` and `DependencyGroup`: Settings related to ATLAS configuration and session metadata.
- `BatchSize`: Number of telemetry samples to be saved to InfluxDb at a time.
- `ThreadCount`: Number of processor threads to be used by the Influx Writer. A value larger than 1 can improve throughput of the writer in a machine that supports multithreading.
- `InitializeDatabase`: True to initialize databases configured in connections section.
- `Connections`: Contains all the database connection information organized by the topic (e.g. Kafka topics.)
- `[TopicName]` : Change the value here depending on the message queue topic you want to subscribe to.
- `InfluxConnections`: Contains all the InfluxDb connection strings. Influx writer supports multiple InfluxDb connections per topic under [labels](#label-supprt). If you plan to use just one InfluxDb instance, use asterisk symbol (*) as a wildcard key. This means, all telemetry data under the topic will be saved to InfluxDb specified in `InfluxDbUrl` regardless of the label.
- `SqlServerConnectionString`: Connection string for session metadata relational database. Influx Writer supports one metadata connection per topic.

#### Logging

Influx Writer has extensive logging and uses Nlog for logger implementation. You can configure logging in the `nlog.config` file or provide your own logging configuration. More information on available configuration options can be found [here](https://github.com/nlog/nlog/wiki/Configuration-file).

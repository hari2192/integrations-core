# Agent Check: Apache Web Server

![Apache Dashboard][1]

## Overview

The Apache check tracks requests per second, bytes served, number of worker threads, service uptime, and more.

## Setup

### Installation

The Apache check is packaged with the Agent. To start gathering your Apache metrics and logs, you need to:

1. [Install the Agent][2] on your Apache servers.

2. Install `mod_status` on your Apache servers and enable `ExtendedStatus`.

### Configuration

<!-- xxx tabs xxx -->
<!-- xxx tab "Host" xxx -->

#### Host

To configure this check for an Agent running on a host:

##### Metric collection

1. Edit the `apache.d/conf.yaml` file in the `conf.d/` folder at the root of your [Agent's configuration directory][3] to start collecting your Apache metrics. See the [sample apache.d/conf.yaml][4] for all available configuration options.

   ```yaml
   init_config:

   instances:
     ## @param apache_status_url - string - required
     ## Status url of your Apache server.
     #
     - apache_status_url: http://localhost/server-status?auto
   ```

2. [Restart the Agent][5].

##### Log collection

_Available for Agent versions >6.0_

1. Collecting logs is disabled by default in the Datadog Agent, you need to enable it in `datadog.yaml`:

   ```yaml
   logs_enabled: true
   ```

2. Add this configuration block to your `apache.d/conf.yaml` file to start collecting your Apache Logs:

   ```yaml
   logs:
     - type: file
       path: /var/log/apache2/access.log
       source: apache
       service: apache
       sourcecategory: http_web_access

     - type: file
       path: /var/log/apache2/error.log
       source: apache
       service: apache
       sourcecategory: http_web_error
   ```

    Change the `path` and `service` parameter values and configure them for your environment. See the [sample apache.d/conf.yaml][4] for all available configuration options.

3. [Restart the Agent][5].

<!-- xxz tab xxx -->
<!-- xxx tab "Docker" xxx -->

#### Docker

To configure this check for an Agent running on a container:

##### Metric collection

Set [Autodiscovery Integrations Templates][15] as Docker labels on your application container:

```yaml
LABEL "com.datadoghq.ad.check_names"='["apache"]'
LABEL "com.datadoghq.ad.init_configs"='[{}]'
LABEL "com.datadoghq.ad.instances"='[{"apache_status_url": "http://%%host%%/server-status?auto"}]'
```

##### Log collection


Collecting logs is disabled by default in the Datadog Agent. To enable it, see the [Docker log collection documentation][16].

Then, set [Log Integrations][17] as Docker labels:

```yaml
LABEL "com.datadoghq.ad.logs"='[{"source": "apache", "service": "<SERVICE_NAME>"}]'
```

<!-- xxz tab xxx -->
<!-- xxx tab "Kubernetes" xxx -->

#### Kubernetes

To configure this check for an Agent running on Kubernetes:

##### Metric collection

Set [Autodiscovery Integrations Templates][18] as pod annotations on your application container. Aside from this, templates can also be configured with [a file, a configmap, or a key-value store][19].

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: apache
  annotations:
    ad.datadoghq.com/apache.check_names: '["apache"]'
    ad.datadoghq.com/apache.init_configs: '[{}]'
    ad.datadoghq.com/apache.instances: |
      [
        {
          "apache_status_url": "http://%%host%%/server-status?auto"
        }
      ]
spec:
  containers:
    - name: apache
```

##### Log collection


Collecting logs is disabled by default in the Datadog Agent. To enable it, see the [Kubernetes log collection documentation][20].

Then, set [Log Integrations][21] as pod annotations. This can also be configured with [a file, a configmap, or a key-value store][22].

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: apache
  annotations:
    ad.datadoghq.com/apache.logs: '[{"source":"apache","service":"<SERVICE_NAME>"}]'
spec:
  containers:
    - name: apache
```


<!-- xxz tab xxx -->
<!-- xxx tab "ECS" xxx -->

#### ECS

To configure this check for an Agent running on ECS:

##### Metric collection

Set [Autodiscovery Integrations Templates][23] as Docker labels on your application container:

```json
{
  "containerDefinitions": [{
    "name": "apache",
    "image": "apache:latest",
    "dockerLabels": {
      "com.datadoghq.ad.check_names": "[\"apache\"]",
      "com.datadoghq.ad.init_configs": "[{}]",
      "com.datadoghq.ad.instances": "[{\"apache_status_url\": \"http://%%host%%/server-status?auto\"}]"
    }
  }]
}
```

##### Log collection


Collecting logs is disabled by default in the Datadog Agent. To enable it, see the [ECS log collection documentation][24].

Then, set [Log Integrations][25] as Docker labels:

```json
{
  "containerDefinitions": [{
    "name": "apache",
    "image": "apache:latest",
    "dockerLabels": {
      "com.datadoghq.ad.logs": "[{\"source\":\"apache\",\"service\":\"<YOUR_APP_NAME>\"}]"
    }
  }]
}
```

<!-- xxz tab xxx -->
<!-- xxz tabs xxx -->

### Validation

[Run the Agent's status subcommand][8] and look for `apache` under the Checks section.

## Data Collected

### Metrics

See [metadata.csv][9] for a list of metrics provided by this check.

### Events

The Apache check does not include any events.

### Service Checks

**apache.can_connect**:<br>
Returns `CRITICAL` if the Agent cannot connect to the configured `apache_status_url`, otherwise returns `OK`.

## Troubleshooting

### Apache status URL

If you are having issues with your Apache integration, it is mostly like due to the Agent not being able to access your Apache status URL. Try running curl for the `apache_status_url` listed in [your `apache.d/conf.yaml` file][4] (include your login credentials if applicable).

- [Apache SSL certificate issues][10]

## Further Reading

Additional helpful documentation, links, and articles:

- [Deploying and configuring Datadog with CloudFormation][11]
- [Monitoring Apache web server performance][12]
- [How to collect Apache performance metrics][13]
- [How to monitor Apache web server with Datadog][14]

[1]: https://raw.githubusercontent.com/DataDog/integrations-core/master/apache/images/apache_dashboard.png
[2]: https://docs.datadoghq.com/agent/
[3]: https://docs.datadoghq.com/agent/guide/agent-configuration-files/#agent-configuration-directory
[4]: https://github.com/DataDog/integrations-core/blob/master/apache/datadog_checks/apache/data/conf.yaml.example
[5]: https://docs.datadoghq.com/agent/guide/agent-commands/#start-stop-and-restart-the-agent
[6]: https://docs.datadoghq.com/agent/kubernetes/integrations/
[7]: https://docs.datadoghq.com/agent/kubernetes/log/
[8]: https://docs.datadoghq.com/agent/guide/agent-commands/#agent-status-and-information
[9]: https://github.com/DataDog/integrations-core/blob/master/apache/metadata.csv
[10]: https://docs.datadoghq.com/integrations/faq/apache-ssl-certificate-issues/
[11]: https://www.datadoghq.com/blog/deploying-datadog-with-cloudformation
[12]: https://www.datadoghq.com/blog/monitoring-apache-web-server-performance
[13]: https://www.datadoghq.com/blog/collect-apache-performance-metrics
[14]: https://www.datadoghq.com/blog/monitor-apache-web-server-datadog
[15]: https://docs.datadoghq.com/agent/docker/integrations/?tab=docker
[16]: https://docs.datadoghq.com/agent/docker/log/?tab=containerinstallation#installation
[17]: https://docs.datadoghq.com/agent/docker/log/?tab=containerinstallation#log-integrations
[18]: https://docs.datadoghq.com/agent/kubernetes/integrations/?tab=kubernetes
[19]: https://docs.datadoghq.com/agent/kubernetes/integrations/?tab=kubernetes#configuration
[20]: https://docs.datadoghq.com/agent/kubernetes/log/?tab=containerinstallation#setup
[21]: https://docs.datadoghq.com/agent/docker/log/?tab=containerinstallation#log-integrations
[22]: https://docs.datadoghq.com/agent/kubernetes/log/?tab=daemonset#configuration
[23]: https://docs.datadoghq.com/agent/docker/integrations/?tab=docker
[24]: https://docs.datadoghq.com/agent/amazon_ecs/logs/?tab=linux
[25]: https://docs.datadoghq.com/agent/docker/log/?tab=containerinstallation#log-integrations

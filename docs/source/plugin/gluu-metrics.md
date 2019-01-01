# Gluu Metrics

Expose metrics in Prometheus exposition format, which can be scrapped by a Prometheus Server. It expose below some metrics

1. UMA and OAuth Client Authentication and Grant
2. UMA Permission Ticket
3. Endpoint and methods

## Configuration

You can configure plugin on **Service**, **Route** and **Global**. There are several possibilities for plugin configuration with services and routes. [More Details](https://docs.konghq.com/0.14.x/admin-api/#precedence).

### Enable plugin on Service

#### 1. Add Service

##### 1.1 Add Service using GG UI

Use [Service section](../admin-gui/#2-add-service) to add service using GG UI.

![3_service_list](../img/3_1_service_list.png)

##### 1.2 Add Service using Kong Admin API

```
$ curl -X POST \
  http://localhost:8001/services \
  -H 'Content-Type: application/json' \
  -d '{
  "name": "<service_name>",
  "url": "http://upstream-api-url.com"
}'
```

#### 2. Configure Plugin

##### 2.1 Configure plugin using GG UI

Use the [Manage Service](../admin-gui/#332-add-plugin) section in GG UI to enable the Gluu Metrics plugin. In the metrics category, there is a Gluu Metrics box. Click on the **+ icon** to enable the plugin.

![14_gluu_metrics_service](../img/14_gluu_metrics_service.png)

After clicking on **+ icon**, you will see the below form.
![14_gluu_metrics_add](../img/14_gluu_metrics_add.png)

##### 2.2 Configure plugin using Kong Admin API

```
$ curl -X POST \
  http://localhost:8001/plugins \
  -H 'Content-Type: application/json' \
  -d '{
  "name": "gluu-metrics",
  "service_id": "<kong_service_object_id>"
}'
```

### Enable plugin Globally

If you enable plugin globally, it will apply for all the services.

#### 1. Configure Plugin

##### 1.1 Configure plugin using GG UI

Use the [Plugin section](../admin-gui/#add-plugin) in GG UI to enable the Gluu Metrics plugin. In the security category, there is a Gluu Metrics box. Click on the **+ icon** to enable the plugin.

![5_plugins_add](../img/14_metrics_plugin_add.png)

After clicking on **+ icon**, you will see the below form.
![11_path_add_uma_service](../img/14_gluu_metrics_add.png)

##### 1.2 Configure plugin using Kong Admin API

```
$ curl -X POST \
  http://localhost:8001/plugins \
  -H 'Content-Type: application/json' \
  -d '{
  "name": "gluu-metrics"
}'
```

### Parameters

Here is a list of all the parameters which can be used in this plugin's configuration.

|Parameters|Default|Description|
|-------------|-------|-----------|
|name||The name of the plugin to use, in this case gluu-metrics.|
|service_id(optional)||The id of the Service which this plugin will target.|
|consumer_id(optional)||The id of the Consumer which this plugin will target.|
|enabled|true|Whether this plugin will be applied.|

## Metrics Endpoint

Metrics are available on the Admin API at the `http://localhost:8001/gluu-metrics` endpoint.

## Available metrics

1. **gluu_endpoint_method**: Endpoint call per service in Kong

2. **gluu_oauth_client_authenticated**: Client(Consumer) OAuth authenticated per service in Kong

3. **gluu_oauth_client_granted**: Client(Consumer) OAuth granted per service in Kong

4. **gluu_uma_client_authenticated**: Client(Consumer) UMA authenticated per service in Kong

5. **gluu_uma_client_granted**: Client(Consumer) UMA granted per service in Kong

6. **gluu_uma_ticket**: UMA Permission Ticket getting per services in Kong

Example of metrics expose by `/gluu-metrics` endpoint.
```
# HELP gluu_endpoint_method Endpoint call per service in Kong
# TYPE gluu_endpoint_method counter
gluu_endpoint_method{endpoint="/comments",method="POST",service="JSON-API"} 40
gluu_endpoint_method{endpoint="/comments/1",method="DELETE",service="JSON-API"} 40
gluu_endpoint_method{endpoint="/comments/1",method="GET",service="JSON-API"} 40
gluu_endpoint_method{endpoint="/posts/1",method="DELETE",service="JSON-API"} 40
gluu_endpoint_method{endpoint="/posts/1",method="GET",service="JSON-API"} 40
gluu_endpoint_method{endpoint="/posts/1",method="GET",service="none-claim-gatering"} 2

# HELP gluu_nginx_metric_errors_total Number of nginx-lua-prometheus errors
# TYPE gluu_nginx_metric_errors_total counter
gluu_nginx_metric_errors_total 0

# HELP gluu_oauth_client_authenticated Client(Consumer) OAuth authenticated per service in Kong
# TYPE gluu_oauth_client_authenticated counter
gluu_oauth_client_authenticated{consumer="@!19CF.B296.532F.83E2!0001!25C1.E1E4!0008!B9EF.436E.5D35.0C58",service="JSON-API"} 200

# HELP gluu_oauth_client_granted Client(Consumer) OAuth granted per service in Kong
# TYPE gluu_oauth_client_granted counter
gluu_oauth_client_granted{consumer="@!19CF.B296.532F.83E2!0001!25C1.E1E4!0008!B9EF.436E.5D35.0C58",service="JSON-API"} 200

# HELP gluu_uma_client_authenticated Client(Consumer) UMA authenticated per service in Kong
# TYPE gluu_uma_client_authenticated counter
gluu_uma_client_authenticated{consumer="@!19CF.B296.532F.83E2!0001!25C1.E1E4!0008!B9EF.436E.5D35.0C58",service="none-claim-gatering"} 1

# HELP gluu_uma_client_granted Client(Consumer) UMA granted per service in Kong
# TYPE gluu_uma_client_granted counter
gluu_uma_client_granted{consumer="@!19CF.B296.532F.83E2!0001!25C1.E1E4!0008!B9EF.436E.5D35.0C58",service="none-claim-gatering"} 1

# HELP gluu_uma_ticket Permission Ticket getting per services in Kong
# TYPE gluu_uma_ticket counter
gluu_uma_ticket{service="none-claim-gatering"} 1
```

## Grafana configuration

Metrics exported by the plugin can be graphed in Grafana using a drop in dashboard: [Gluu-Metrics-Grafana.json](https://github.com/GluuFederation/gluu-gateway/blob/version_4.0.0/setup/templates/Gluu-Metrics-Grafana.json).

1. Install **Grafana v5.4.2**
2. Add Datasource
    - Start grafana service
    - Open in browser(Default port 3000. http://localhost:3000)
    - Configuration > Data sources > Add data source > Prometheus
    - Add prometheus server URL
    ![5_plugins_add](../img/14_grafana_datasource.png)
3. Import JSON: [Gluu-Metrics-Grafana.json](https://github.com/GluuFederation/gluu-gateway/blob/version_4.0.0/setup/templates/Gluu-Metrics-Grafana.json)
    - Go to home pade
    - Click on `New dashboard` on top left corner.
    - Click on `import dashboard`
    - Upload .json file

## Prometheus server configuration

The Gluu-Metrics plugin exposes metrics at `gluu-metrics` endpoint. You need to config prometheus server to listen this endpoint.

1. Install **prometheus server v2.6.0**
2. Add our endpoint in prometheus.yml in **scrape_configs** section.
   ```
     - job_name: gluu
       metrics_path: /gluu-metrics
       static_configs:
       - targets: [your-kong-host-server.com:8001]
   ```
3. Restart prometheus server.
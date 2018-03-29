# Admin GUI Portal - Konga Guide

### 1. Dashboard

Dashboard section shows all application configuration details. You can see oxd and client details used by konga.
![dashboard](img/1_dashboard.png)

### 2. Info

Info section shows generic details about the kong node.
![info](img/2_info.png)

### 3. APIS

The API object describes an API that's being exposed by Kong. Kong needs to know how to retrieve the API when a consumer is calling it from the Proxy port. Each API object must specify a request host, a request path or both. Kong will proxy all requests to the API to the specified upstream URL.
![apis](img/3_apis.png)

Add your API by using `+ ADD NEW API` button. Add form shows details of every field.
![api_add](img/3_api_add.png)

For Add UMA RS plugin click on `SECURITY` option in API's list.
![api_uma_rs](img/3_add_uma_rs.png)

#### Manage plugins in APIS.

You need to go in manage mode in API section by click on pencil icon in API's list.

API's Plugin list
![api_plugin_list](img/3_2_api_plugin_list.png)

Add Plugin in API
![api_uma_rs](img/3_1_add_plugin_api.png)

### 4. Consumers

The Consumer object represents a consumer - or a user - of an API. You can either rely on Kong as the primary datastore, or you can map the consumer list with your database to keep consistency between Kong and your existing primary datastore.
![consumers](img/4_consumers.png)

Add consumers by using `+ CREATE CONSUMER` button. Add form shows details of every field.
![consumers_add](img/4_customer_add.png)

#### Consumer credential configuration

Some plugins are consumer based. It stores some plugin configuration in consumer credential. You need to go in consumer credential section by clicking on consumer `username`.
![consumer_credential_list](img/4_1_consumer_credential_list.png)

### 5. Plugins

A Plugin entity represents a plugin configuration that will be executed during the HTTP request/response workflow, and it's how you can add functionalities to APIs that run behind Kong, like Authentication or Rate Limiting for example.
![plugins](img/5_plugins.png)

Add Plugins by using `+ ADD GLOBAL PLUGINS` button.
![plugins_add](img/5_plugins_add.png)

### 6. Upstreams

The upstream object represents a virtual hostname and can be used to loadbalance incoming requests over multiple services (targets). So for example an upstream named service.v1.xyz with an API object created with an upstream_url=https://service.v1.xyz/some/path. Requests for this API would be proxied to the targets defined within the upstream.
![upstreams](img/6_upstream.png)

Add Plugins by using `+ CREATE UPSTREAM` button.
![plugins_add](img/6_upstream_add.png)

### 7. CERTIFICATE

A certificate object represents a public certificate/private key pair for an SSL certificate. These objects are used by Kong to handle SSL/TLS termination for encrypted requests. Certificates are optionally associated with SNI objects to tie a cert/key pair to one or more hostnames.
![cert](img/7_cert.png)

Add Plugins by using `+ CREATE CERTIFICATE` button.
![cert_add](img/7_cert_add.png)

### 8. Connections

Create connections to Kong Nodes and select the one to use by clicking on the respective star icon.
![conn](img/8_conn.png)

Add Plugins by using `+ NEW CONNECTION` button.
![conn_add](img/8_conn_add.png)

### 9. Snapshots

Take snapshots of currently active nodes.
All APIs, Plugins, Consumers, Upstreams and Targets will be saved and available for later import.
![snapshot](img/9_snapshot.png)

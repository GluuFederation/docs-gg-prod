# Admin GUI Portal - Konga Guide

## Dashboard
The Dashboard section is divided into subsections which show application configuration details. 

In the Global Info you can see oxd and client details used by Konga. If you want to check the version of the currently used oxd server or the address of the Gluu Server, all the necessary information is provided here. OXD Id Client, Id of OXD Id, Setup client OXD Id, Client Id and Client Secret are the credentials created during the Gluu Gateway installation and setup. 

By default, two oxd clients are created during the installation and setup of the Gluu Gateway. You can always check their activity and billing status by clicking the green button in the Pricing subsection, which will take you to the oxd ecommerce platform. The Pricing table also provides more information on the oxd usage and billing.

The Gateway and Database Info show information on the gateway itself and the used postgres database, respectively. 

The Plugins section displays all the plugins supported by the Gluu Gateway. When inactive, a plugin is shown as gray. If you add a plugin to an API/a Consumer or set a global one, its name will turn green on the dashboard.

The remaining subsections of Requests, Connections and Timers show real-time metrics on the Gluu Gateway health.  

![dashboard](img/1_dashboard.png)

## Info

The Info section shows generic details about the Kong node.
![info](img/2_info.png)

## APIs

The API object describes an API that's being exposed by Kong. Kong needs to know how to retrieve the API when a consumer is calling it from the Proxy port. Each API object must specify some combination of `hosts`, `uris`, and `methods`. Kong will proxy all requests to the API to the specified upstream URL. 

!!! Note
    The `SECURITY` option is for the [gluu-oauth2-rs plugin](https://gluu.org/docs/gg/3.1.3/plugin/gui/#gluu-oauth-20-uma-rs-plugin) configuration. Saving any configuration of paths, HTTP methods and scopes in the UMA Resources means adding the gluu-oauth2-rs plugin to the given API, which is necessary for successful execution of [UMA and Mix flows](https://gluu.org/docs/gg/3.1.3/#uma-mode).
    
![apis](img/3_apis.png)

### Add an API

Add your API by using the `+ ADD NEW API` button in the API section.

![api_add](img/3_api_add.png)

Attributes of an API object are listed below.

| **FORM PARAMETER** | **DESCRIPTION** |
|-----------|-------------|
| **name** | The API name. |
| **hosts** *(semi-optional)* | A comma-separated list of domain names that point to your API. For example: `example.com`. At least one of `hosts`, `uris`, or `methods` should be specified. |
| **uris** *(semi-optional)* | A comma-separated list of URI's prefixes that point to your API. For example: `/my-path`. At least one of `hosts`, `uris`, or `methods` should be specified. |
| **methods** *(semi-optional)* | A comma-separated list of HTTP methods that point to your API. For example: `GET`,`POST`. At least one of `hosts`, `uris`, or `methods` should be specified. |
| **upstream_url** | The base target URL that points to your API server. This URL will be used for proxying requests. For example: `https://example.com`. |
| **strip_uri** *(optional)* | When matching an API via one of the uri's prefixes, strip that matching prefix from the upstream URI to be requested. Default: `true`. |
| **preserve_host** *(optional)* | When matching an API via one of the `hosts` domain names, make sure the request `Host` header is forwarded to the upstream service. By default, this is `false`, and the upstream `Host` header will be extracted from the configured `upstream_url`. |
| **retries** *(optional)* | The number of retries to execute upon failure to proxy. The default is `5`. |
| **upstream_connect_timeout** *(optional)* | The timeout in milliseconds for establishing a connection to your upstream service. Defaults to `60000`. |
| **upstream_send_timeout** *(optional)* | The timeout in milliseconds between two successive write operations for transmitting a request to your upstream service. Defaults to `60000`. |
| **upstream_read_timeout** *(optional)* | The timeout in milliseconds between two successive read operations for transmitting a request to your upstream service. Defaults to `60000`. |
| **https_only** *(optional)* | Enable if you wish to only serve an API through HTTPS, on the appropriate port (`443` by default). Default: `false`. |
| **http_if_terminated** *(optional)* | Consider the `X-Forwarded-Proto` header when enforcing HTTPS-only traffic. Default: `false`. |

### Manage APIS

You can edit an API and manage its plugins by clicking on the pencil icon on the API list. There are two sections.

**API Details:** This section is used to view and edit your API.

![api_details](img/3_3_api_details.png)

**Plugins:** This section is used to view the list of added Plugins and add a new Plugin. If you want to switch a plugin on/off, just use the toggle bar. Editing a plugin’s details is possible after clicking on its name on the list. You can also delete a plugin by clicking on the DELETE button.

* The Plugin list
  
  ![api_plugin_list](img/3_2_api_plugin_list.png)

* Add a Plugin by clicking the plus icon next to a plugin’s name
  
  ![add_plugin_api](img/3_1_add_plugin_api.png)

## Consumers

The Consumer object represents a consumer - or a user - of an API. You can either rely on Kong as the primary datastore, or you can map the consumer list with your database to keep consistency between Kong and your existing primary datastore.

![consumers](img/4_consumers_new.png)

Add Consumers by using the `+ CREATE CONSUMER` button. The creation form shows details of every field.

![consumers_add](img/4_customer_add.png)

### Consumer credentials configuration

Some plugins are consumer-based and store some plugin configuration in consumer credentials. You need to go to the consumer credentials section by clicking on a consumer's `username`.

![consumer_credential_list](img/4_1_consumer_credential_list.png)

## Plugins

A Plugin entity represents a plugin configuration that will be executed during the HTTP request/response workflow. It also enables the user to add functionalities to APIs that run behind Kong, e.g. Authentication or Rate Limiting. You can read about the available Kong Plugins [here](https://konghq.com/plugins/). 

Plugins added in this section of the Gluu Gateway will be applied to all APIs. If you need to add plugins to a specific API, you can do it in the APIs section.
If you need to add plugins to a specific Consumer, you can do it in the respective Consumer page.

![plugins](img/5_plugins.png)

Add Plugins by using the `+ ADD GLOBAL PLUGINS` button.

![plugins_add](img/5_plugins_add.png)

## Upstreams

The Upstream object represents a virtual hostname and can be used to load balance incoming requests over multiple services (targets), e.g. an upstream named service.v1.xyz with an API object created with an upstream_url=https://service.v1.xyz/some/path. Requests for this API would be proxied to the targets defined within the upstream.

![upstreams](img/6_upstream.png)

Add Upstreams by using the `+ CREATE UPSTREAM` button.

![plugins_add](img/6_upstream_add.png)

You can modify the details of your Upstream by clicking the `DETAILS` button next to its name. While changing the number of slots, make sure that the number corresponds to the `Orderlist` and exactly matches its value. For instance, if you want 999 slots, delete slot 1000, for 998 - delete 999 and 1000, etc.).

The `Targets` section enables target addition. Because the upstream maintains a history of target changes, the targets cannot be deleted from the list or modified. To disable a target, post a new one with weight=0 or click on the `DELETE` button next to its name, which will automatically assign 0 to its weight. 


## CERTIFICATE

A Certificate object represents a public certificate/private key pair for an SSL certificate. These objects are used by Kong to handle SSL/TLS termination for encrypted requests. Certificates are optionally associated with SNI objects to tie a cert/key pair to one or more hostnames.

![cert](img/7_cert.png)

Add Certificates by using the `+ CREATE CERTIFICATE` button.

![cert_add](img/7_cert_add.png)

## Connections

Create connections to Kong nodes and select the one to use by clicking on the respective star icon.

![conn](img/8_conn.png)

Add Connections by using the `+ NEW CONNECTION` button.

![conn_add](img/8_conn_add.png)

## Snapshots

Take snapshots of currently active nodes.
All APIs, Plugins, Consumers, Upstreams and Targets will be saved and available for later import.

![snapshot](img/9_snapshot.png)

## Settings

You can set dashboard refresh interval and logout session timeout in settings section.

![settings](img/10_settings.png)

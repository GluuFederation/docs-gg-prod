# Admin GUI Portal - Konga Guide

## Dashboard
The Dashboard section is divided into subsections that show application configuration details. 

In the Global Info you can see oxd and client details used by Konga. If you want to check the version of the currently used oxd Server or the address of the Gluu Server, all the necessary information is provided here. ID of oxd ID, Client ID and Client Secret are the credentials created during the Gluu Gateway installation and setup.

By default, one oxd clients are created during the installation and setup of the Gluu Gateway.

The Gateway and Database Info show information on the gateway itself and the used postgres database, respectively. 

The Plugins section displays all the plugins supported by the Gluu Gateway. When inactive, a plugin is shown as gray. If you add a plugin to an API/a Consumer or set a global one, its name will turn green on the dashboard.

The remaining subsections of Requests, Connections and Timers show real-time metrics on the Gluu Gateway health.  

![dashboard](img/1_dashboard.png)

## Info

The Info section shows generic details about the Kong node.
![info](img/2_info.png)

## Services

### Service List

Service entities, as the name implies, are abstractions of each of your own upstream services. Examples of Services would be a data transformation microservice, a billing API, etc. [More details](https://docs.konghq.com).   
    
![3_service_list](img/3_1_service_list.png)

| Tools | Details |
|---|-----|
| **+ ADD NEW SERVICE** | This button is used to add a new service.|
| **Gluu Security** | This column only shows the added Gluu plugins.|
| **Edit Button** | This button is used to edit a service, configure route and configure plugins of the selected service. You can click on **service name** to edit the service.|
| **Delete Button** | This button is used to delete the selected service. |
   
### Add Service

Add your Service using the `+ ADD NEW SERVICE` button.

![3_2_add_service](img/3_2_add_service.png)

### Manage Service

You can edit a Service and manage its plugins by clicking on the pencil icon on the Service list. There are four sections.

#### Service Details
This section is used to view and edit your Service.

![3_3_service_details](img/3_3_service_details.png)

#### Routes
This section is used to manage the route within selected service.

![3_4_service_route](img/3_4_service_route.png)

| Tools | Details |
|---|-----|
| **+ ADD ROUTE** | This button is used to add a new route.|
| **Edit Button** | This button is used to edit a route, configure a route and configure plugins of the selected service.|
| **Delete Button** | This button is used to delete the selected route. |

#### Plugins
This section is used to view the list of added Plugins and add a new Plugin.

##### Plugin list
  
  ![3_5_service_plugins](img/3_5_service_plugins.png)

  | Tools | Details |
  |---|-----|
  | **+ ADD PLUGIN** | This button is used to add a plugin.|
  | **Edit Plugin** | Click on a plugin's name to edit its configuration.|
  | **Delete Button** | This button is used to delete selected route. |
  | **ON/OFF Switch** |If you want to switch a plugin on/off, just use the toggle bar.|

##### Add Plugin

  Add a Plugin by clicking the `+` icon next to the plugin’s name.
  
  ![3_6_add_plugins](img/3_6_add_plugins.png)

#### Eligible consumers

  This section is for the ACL Kong plugin, which restricts access to an API by whitelisting or blacklisting consumers using arbitrary ACL group names. It shows the list of consumers which are configured with ACL groups.

  ![3_7_eligible_consumers](img/3_7_eligible_consumers.png)

## Routes

### Route List

The Route entities define rules to match client requests. Each Route is associated with a Service, and a Service may have multiple Routes associated with it. Every request matching a given Route will be proxied to its associated Service. [More details](https://docs.konghq.com).

![4_1_route_list](img/4_1_route_list.png)

| Tools | Details |
|---|-----|
| **Gluu Security** | This column only shows the added Gluu plugins.|
| **Edit Button** | This button is used to edit a Route and configure its plugins. You can click on **ROUTE ID** to edit the Route.|
| **Delete Button** | This button is used to delete the selected Route. |

### Add Route

[Use Service section to add new route](#routes).

### Manage Route

You can edit a Route and manage its plugins by clicking on the pencil icon on the Route list. There are three sections.

#### Route Details
This section is used to view and edit your Route.

![4_2_route_details](img/4_2_route_details.png)

#### Plugins
This section is used to view the list of added Plugins and add a new Plugin.

##### Plugin list

  ![4_3_route_plugin](img/4_3_route_plugin.png)

  | Tools | Details |
  |---|-----|
  | **+ ADD PLUGIN** | This button is used to add plugin.|
  | **Edit Plugin** | Click on plugin name to edit plugin configurations.|
  | **Delete Button** | This button is used to delete selected route. |
  | **ON/OFF Switch** |If you want to switch a plugin on/off, just use the toggle bar.|

##### Add Plugin

  Add a Plugin by clicking the plus icon next to a plugin’s name.

  ![4_4_add_plugins](img/3_6_add_plugins.png)

#### Eligible consumers

  This section is for ACL kong plugin which Restrict access to an API by whitelisting or blacklisting consumers using arbitrary ACL group names. It shows the list of consumers which is configure with ACL Groups.

  ![4_5_route_eligible_consumer](img/4_5_route_eligible_consumer.png)

## CONSUMERS

The Consumer object represents a consumer - or a user - of a Service. You can either rely on Kong as the primary datastore, or you can map the consumer list with your database to keep consistency between Kong and your existing primary datastore.

![consumers](img/4_consumers.png)

Add Consumers by using the `+ CREATE CONSUMER` button.

![consumers_add](img/4_customer_add.png)

| Fields | Details |
|---|-----|
| **Consumer Name** | It is Kong Consumer Username, Identifier used by Kong for the client. Should contain no spaces or special characters.|
| **Gluu Client Id** | It is Kong Consumer Custom Id, Used to correlate an access token with a Kong consumer. You must create a client before you can register it here as a way to identify a consumer.|

### Manage Consumer

Click on **Consumer Name** of consumer to manage consumer. You can edit, manage ACLs plugin group and add plugins in manage consumer.

#### Details

You can see and edit the selected consumer details.

![4_edit_consumer](img/4_edit_consumer.png)

#### Groups

You can create a group for ACL plugin. which whitelist and blacklist consumer according to ACL plugin configuration.

![4_consumer_groups](img/4_consumer_groups.png)

#### Plugins

Some plugins provide facility to configure plugin with specific consumer. You can use this section to configure plugin for selected consumer. It will add plugin as global plugin which will apply for every service and route.

![4_consumer_plugin](img/4_consumer_plugin.png)

### Create Client

Click on `+ CREATE CLIENT` button to create OP client. It will create client with `openid`, `oxd` scope and with `client_credentials` grant type.

![4_consumer_client](img/4_consumer_client.png)

| Fields | Details |
|---|-----|
| **Client Name**(required) |Use to create client with name.|
| **Client Id**(optional) |Use any existing OP Client's client_id. If you leave it blank, OXD server will create new client in your OP server.|
| **Client Secret**(optional) |Use any existing OP Client's client_secret. If you leave it blank, OXD server will create new client in your OP server.|
| **Access Token as JWT**(optional) |It will create client with `Access Token as JWT:true`. It is used to return access token as JWT. Gluu OAuth PEP plugin support access token as JWT.|
| **RPT as JWT**(optional) |It will create client with `RPT as JWT:true`. It is used to return access token(RPT) as JWT. Gluu UMA PEP support access token(RPT) as JWT.|
| **Token signing algorithm**(optional) |It is used to set default token signing algorithm for client. It is used for both tokens OAuth access token and UMA RPT token. Currently plugins supports only 3 Algorithm **RS256**, **RS384** and **RS512**.|

## PLUGINS

A Plugin entity represents a plugin configuration that will be executed during the HTTP request/response lifecycle. It is how you can add functionalities to Services that run behind Kong, like Authentication or Rate Limiting for example.

Plugins added in this section of the Gluu Gateway will be applied to all SERVICEs and ROUTEs. If you need to add plugins to a specific SERVICEs or ROUTEs, you can do it in the [SERVICEs](#services) or [ROUTEs](#routes) section.
If you need to add plugins to a specific Consumer, you can do it in the respective [Consumer page](#consumers).

### Plugin list

![5_plugins](img/5_plugins.png)

### Add Plugin

Add Plugins by using the `+ ADD GLOBAL PLUGINS` button.

![5_plugins_add](img/5_plugins_add.png)

## UPSTREAMS

The upstream object represents a virtual hostname and can be used to loadbalance incoming requests over multiple services (targets). So for example an upstream named service.v1.xyz for a Service object whose host is service.v1.xyz. Requests for this Service would be proxied to the targets defined within the upstream.

![6_upstream](img/6_upstream.png)

Add Upstreams by using the `+ CREATE UPSTREAM` button.

![6_upstream_add](img/6_upstream_add.png)

You can modify the details of your Upstream by clicking the `DETAILS` button next to its name.

![6_upstream_details](img/6_upstream_details.png)

The `Targets` section is for manage targets. A target is an ip address/hostname with a port that identifies an instance of a backend service. Every upstream can have many targets, and the targets can be dynamically added. Changes are effectuated on the fly.

![6_upstream_targets](img/6_upstream_targets.png)

## CERTIFICATES

A Certificate object represents a public certificate/private key pair for an SSL certificate. These objects are used by Kong to handle SSL/TLS termination for encrypted requests. Certificates are optionally associated with SNI objects to tie a cert/key pair to one or more hostnames.

![cert](img/7_cert.png)

Add Certificates by using the `+ CREATE CERTIFICATE` button.

![cert_add](img/7_cert_add.png)

## CONNECTIONS

Create connections to Kong nodes and select the one to use by clicking on the respective star icon.

![conn](img/8_conn.png)

Add Connections by using the `+ NEW CONNECTION` button.

![conn_add](img/8_conn_add.png)

## SNAPSHOTS

Take snapshots of currently active nodes.
All SERVICEs, ROUTEs, Plugins, Consumers, Upstreams and Targets will be saved and available for later import.

### List

It shows the list of snapshots.

![9_snapshot](img/9_snapshot.png)

### Take Snapshot

![9_take_snapshot](img/9_take_snapshot.png)

### Details

Click on `Details` option in snapshot list view to see details of snapshot.

![9_snapshot_details](img/9_snapshot_details.png)

You can restore objects by clicking on `RESTORE` button.

![9_snapshot_restore](img/9_snapshot_restore.png)

You can export data by clicking on `EXPORT` button.

### Scheduled tasks

This is used to schedule task which periodically take snapshot.

![9_snapshot_scheduled_list](img/9_snapshot_scheduled_list.png)

Create schedule task using `ADD SCHEDULE` button.

![9_snapshot_scheduled_add](img/9_snapshot_scheduled_add.png)

## SETTINGS

You can set dashboard refresh interval, logout session timeout and login restriction in settings section.

![settings](img/10_settings.png)

### General settings

|Setting|Description|
|-------|-----------|
|Dashboard refresh interval|The interval in milliseconds at which the Dashboard data will refresh. Default is 5000 miliseconds.|
|Logout session timeout|The interval in minutes at which you will be logged out after idle time. Default is 5000 minute.|

### Login restrictions

|Setting|Description|
|-------|-----------|
|Allow only admin user to login.|If enabled, only OP Users with **admin** role(permission) is allowed to log in to Gluu Gateway UI. Follow [CE Docs](https://gluu.org/docs/ce/user-management/user-registration/#adding-attributes-to-registration) to add role in user.|
# Gluu UMA Auth and UMA PEP
## Overview
The UMA Auth and UMA PEP is used client authentication and to enforce the presence of UMA scopes for access to resources protected by the Gateway. UMA scopes and policies are defined in an external UMA Authorization Server (AS) -- in most cases the Gluu Server. The Gateway and AS leverage the oxd UMA middleware service for communication. 

There are two plugins for OAuth security.

   1. **gluu-uma-auth**: Authenticate client by UMA RPT Token. The plugin priority is `998`.
   1. **gluu-uma-pep**: Authorization by UMA Scope security. The plugin priority is `995`.

The plugin supports two tokens:  

   1. **Default Access Token**: The plugin will authenticate the token using introspection.   
   1. **Access Token as JWT**: The plugin will authenticate the token using JWT verify. Currently three algorithms are supported: **RS256**, **RS384** and **RS512**.

## Configuration

Plugins can be configured at the **Service**, **Route** or **Global** level. There are several possibilities for plugin configuration with services and routes. For information on plugin precedence, [read the Kong docs](https://docs.konghq.com/0.14.x/admin-api/#precedence).

!!! Important
    During plugin configuration, the **GG UI** creates a new OP Client if the **oxd ID** is left blank. However, if configuring with the **Kong Admin API**, existing client credentials must be used.

!!! Important
    konga.log also shows the curl commands for all API requests to Kong and oxd made by the Konga GUI. This curl command can be used to automate configuration instead of using the web interface.

### Service Level

#### Add Service using GG UI

Use the [Service section](../admin-gui/#add-service) of the GG UI doc to add a service using GG UI.

![3_service_list](../img/3_1_service_list.png)

#### Add Service using Kong Admin API

```
$ curl -X POST \
  http://<kong_hostname>:8001/services \
  -H 'Content-Type: application/json' \
  -d '{
  "name": "<service_name>",
  "url": "http://upstream-api-url.com"
}'
```

#### Configure Service Plugin using GG UI

Use the [Manage Service](../admin-gui/#manage-service) section in GG UI to enable the Gluu UMA PEP plugin. In the security category, there is a Gluu UMA PEP box. Click on the **+** icon to enable the plugin.

![11_path_uma_service](../img/11_path_uma_service.png)

Clicking on the **+** icon will bring up the below form.

!!! Important
    You need to set the `Anonymous` consumer because it is used to by pass gluu-uma-auth authentication and help to get the ticket from gluu-uma-pep. In below form use `+` button front on anonymous field to add and configure consumer. You just need to copy consumer id and past it to anonymous field.

![11_path_add_uma_service](../img/uma-auth-pep-form.png)

#### Configure Service Plugin using Kong Admin API

!!! Note
    Use [OXD API](https://gluu.org/docs/oxd/4.0/) for [client registration](https://gluu.org/docs/oxd/4.0/api/#register-site) and [UMA resource registration](https://gluu.org/docs/oxd/4.0/api/#uma-rs-protect-resources).

Configuration for `gluu-uma-auth`

```
$ curl -X POST \
  http://<kong_hostname>:8001/plugins \
  -H 'Content-Type: application/json' \
  -d '{
  "name": "gluu-uma-auth",
  "config": {
    "oxd_url": "<your_oxd_server_url>",
    "op_url": "<your_op_server_url>",
    "oxd_id": "<oxd_id>",
    "client_id": "<client_id>",
    "client_secret": "<client_secret>",
    "hide_credentials": <false|true>,
    "anonymous": "<anonymous_consumer_id>"
  },
  "service_id": "<kong_service_object_id>"
}'
```

Configuration for `gluu-uma-pep`

```
$ curl -X POST \
  http://<kong_hostname>:8001/plugins \
  -H 'Content-Type: application/json' \
  -d '{
  "name": "gluu-uma-pep",
  "config": {
    "oxd_url": "<your_oxd_server_url>",
    "op_url": "<your_op_server_url>",
    "oxd_id": "<oxd_id>",
    "client_id": "<client_id>",
    "client_secret": "<client_secret>",
    "uma_scope_expression": [
      {
        "path": "/posts",
        "conditions": [
          {
            "httpMethods": [
              "GET"
            ]
          }
        ]
      }
    ],
    "deny_by_default": <false|true>
  },
  "service_id": "<kong_service_object_id>"
}'
```

!!! Note
    Plugin don't need `scope_expression` inside `conditions` because rule and expression is check and register at AS side. 

!!! Note
    Kong does not allow proxying using only a service object--this feature requires a route. At minimum, one service is needed to register an Upstream API and one route is needed for proxying.

### Route Level

#### Add Route using GG UI

Use [Manage Service Section](../admin-gui/#service-routes) to add a route using GG UI.

![3_4_service_route](../img/3_4_service_route.png)

#### Add Route using Kong Admin API

```
$ curl -X POST \
    http://<kong_hostname>:8001/routes \
    -H 'Content-Type: application/json' \
    -d '{
    "hosts": [
      "<your_host.com>"
    ],
    "service": {
      "id": "<kong_service_object_id>"
    }
  }'
```

!!! Information
    There are several possibilities for what to put in the `hosts` field. One technique is to send the request to a proxy. See more information and possibilities in the [Proxy reference](https://docs.konghq.com/0.14.x/proxy/) Kong Documents.

#### Configure Route Plugin using GG UI

Use the [Manage Route](../admin-gui/#manage-route) section in the GG UI to enable the Gluu UMA PEP plugin. In the security category, there is a Gluu UMA PEP box. Click on the **+** icon to enable the plugin.

![12_path_uma_route](../img/12_path_uma_route.png)

Clicking on the **+** icon will bring up the below form.
![12_path_add_uma_route](../img/uma-auth-pep-form.png)

#### Configure Route Plugin using Kong Admin API

!!! Note
    Use [OXD API](https://gluu.org/docs/oxd/4.0/) for [client registration](https://gluu.org/docs/oxd/4.0/api/#register-site) and [UMA resource registration](https://gluu.org/docs/oxd/4.0/api/#uma-rs-protect-resources).

Configuration for `gluu-uma-auth`

```
$ curl -X POST \
  http://<kong_hostname>:8001/plugins \
  -H 'Content-Type: application/json' \
  -d '{
  "name": "gluu-uma-auth",
  "config": {
    "oxd_url": "<your_oxd_server_url>",
    "op_url": "<your_op_server_url>",
    "oxd_id": "<oxd_id>",
    "client_id": "<client_id>",
    "client_secret": "<client_secret>",
    "hide_credentials": <false|true>,
    "anonymous": "<anonymous_consumer_id>"
  },
  "route_id": "<kong_service_object_id>"
}'
```

Configuration for `gluu-uma-pep`

```
$ curl -X POST \
  http://<kong_hostname>:8001/plugins \
  -H 'Content-Type: application/json' \
  -d '{
  "name": "gluu-uma-pep",
  "config": {
    "oxd_url": "<your_oxd_server_url>",
    "op_url": "<your_op_server_url>",
    "oxd_id": "<oxd_id>",
    "client_id": "<client_id>",
    "client_secret": "<client_secret>",
    "uma_scope_expression": [
      {
        "path": "/posts",
        "conditions": [
          {
            "httpMethods": [
              "GET"
            ]
          }
        ]
      }
    ],
    "deny_by_default": <false|true>
  },
  "route_id": "<kong_service_object_id>"
}'
```

### Global Plugin

A global plugin will apply to all services and routes.

#### Configure Global Plugin using GG UI

Use the [Plugin section](../admin-gui/#add-plugin) in the GG UI to enable the Gluu UMA PEP plugin. In the security category, there is a Gluu UMA PEP box. Click on the **+** icon to enable the plugin.

![5_plugins_add](../img/5_uma_plugins_add.png)

Clicking on the **+** icon will bring up the below form.
![11_path_add_uma_service](../img/uma-auth-pep-form.png)

#### Configure Global Plugin using Kong Admin API

!!! Note
    Use [OXD API](https://gluu.org/docs/oxd/4.0/) for [client registration](https://gluu.org/docs/oxd/4.0/api/#register-site) and [UMA resource registration](https://gluu.org/docs/oxd/4.0/api/#uma-rs-protect-resources).

Configuration for `gluu-uma-auth`

```
$ curl -X POST \
  http://<kong_hostname>:8001/plugins \
  -H 'Content-Type: application/json' \
  -d '{
  "name": "gluu-uma-auth",
  "config": {
    "oxd_url": "<your_oxd_server_url>",
    "op_url": "<your_op_server_url>",
    "oxd_id": "<oxd_id>",
    "client_id": "<client_id>",
    "client_secret": "<client_secret>",
    "hide_credentials": <false|true>,
    "anonymous": "<anonymous_consumer_id>"
  }
}'
```

Configuration for `gluu-uma-pep`

```
$ curl -X POST \
  http://<kong_hostname>:8001/plugins \
  -H 'Content-Type: application/json' \
  -d '{
  "name": "gluu-uma-pep",
  "config": {
    "oxd_url": "<your_oxd_server_url>",
    "op_url": "<your_op_server_url>",
    "oxd_id": "<oxd_id>",
    "client_id": "<client_id>",
    "client_secret": "<client_secret>",
    "uma_scope_expression": [
      {
        "path": "/posts",
        "conditions": [
          {
            "httpMethods": [
              "GET"
            ]
          }
        ]
      }
    ],
    "deny_by_default": <false|true>
  }
}'
```

### Parameters

Here is a list of all the parameters which can be used in this plugin's configuration.

1. Gluu-UMA-Auth

     | field | Default | Description |
     |-------|---------|-------------|
     |**op_url**||The URL of your OP server. Example: https://op.server.com|
     |**oxd_url**||The URL of your oxd server. Example: https://oxd.server.com|
     |**oxd_id**|| The ID for an existing client, used to introspect the token. If left blank, a new client will be registered dynamically |
     |**client_id**|| An existing client ID, used to get a protection access token to access the introspection API. Required if an existing oxd ID is provided.|
     |**client_secret**|| An existing client secret, used to get protection access token to access the introspection API. Required if an existing oxd ID is provided.|
     |**anonymous**||An required string (consumer Id) value to use as an “anonymous” consumer if authentication fails. You need to set the Anonymous consumer because it is used to by pass gluu-uma-auth authentication and help to get the ticket from gluu-uma-pep. |
     |**pass_credentials**|pass|It allows 3 values. `pass`, `hide` and `phantom_token`. Used to operate the authorization header from the upstream service as per configuration. In `phantom_token` case, plugin will replace bearer token with new generated JWT(with introspection result) token, so for outside there is bearer token and JWT for internal use.|

2. Gluu-UMA-PEP 

     | field | Default | Description |
     |-------|---------|-------------|
     |**op_url**||The URL of your OP server. Example: https://op.server.com|
     |**oxd_url**||The URL of your oxd server. Example: https://oxd.server.com|
     |**oxd_id**|| The ID for an existing client, used to introspect the token. If left blank, a new client will be registered dynamically |
     |**client_id**|| An existing client ID, used to get a protection access token to access the introspection API. Required if an existing oxd ID is provided.|
     |**client_secret**|| An existing client secret, used to get protection access token to access the introspection API. Required if an existing oxd ID is provided.|
     |**uma_scope_expression**|| Used to add scope security on an UMA scope token.|
     |**deny_by_default**| true | For paths not protected by UMA scope expressions. If true, denies unprotected paths.|
     |**require_id_token**|false|It use when you configure `gluu-openid-connect` plugin. This is for Push Claim token. if it is true then it will use id_token for push claim token for getting RPT|
     |**obtain_rpt**|false|It is used to get RPT when you configure `gluu-openid-connect` plugin with `gluu-uma-pep`|
     |**claims_redirect_path**||It use when you configure `gluu-openid-connect` plugin. Claims redirect URL in claim gathering flow for your OP Client. You just need to set path here like `/claim-callback` but you need to register OP Client with full URL like `https://kong.proxy.com/claim-callback`. GG UI creates OP client for you and also configure the `gluu-openid-connect` and `gluu-uma-pep` plugin.|
     |**redirect_claim_gathering_url**|false|It use when you configure `gluu-openid-connect` plugin. It used to tell plugin that if `need_info` response comes in claim gathering situation then redirect it for claim gathering.|
     |**method_path_tree**||It is for plugin internal use. We use it for tree level matching for dynamic paths which registered in `uma_scope_expression`| 


!!! Note
    GG UI can create a dynamic client. However, if the Kong Admin API is used for plugin configuration, it requires an existing client using the oxd API, then passing the client's credentials to the Gluu-UMA-PEP plugin.

#### Phantom Token

In some cases there is requirement that bearer token for outside of the network and JWT token for the internal network. Check [here](../common-features/#phantom-token) for more details.

#### UMA Scope Expression

The UMA Scope Expression is a JSON expression, used to register the resources in a resource server. See more details in the [Gluu Server docs](https://gluu.org/docs/ce/api-guide/uma-api/#uma-permission-registration-api).

For example, to protect an API:

```
[
  {
    "path": "/images",
    "conditions": [
      {
        "httpMethods": [
          "GET"
        ],
        "scope_expression": {
          "rule": {
            "and": [
              {
                "var": 0
              },
              {
                "or": [
                  {
                    "var": 1
                  },
                  {
                    "var": 2
                  }
                ]
              }
            ]
          },
          "data": [
            "admin",
            "employee",
            "ouside"
          ]
        }
      }
    ]
  }
]
```

![13_uma_scope_expression](../img/13_uma_scope_expression.png)

At runtime, the plugin sends a request to the RS with an RPT token and checks the permission for requested resources.

#### Dynamic Resource Protection

There are 3 elements to make more dynamic path registration and protection. Check [here](../common-features/#dynamic-resource-protection) for more details.

## Usage

### Create Client

Create a client using [create client consumer section](../../admin-gui/#create-client). Use the oxd `register-site` API to create a client.

### Create Consumer

A client credential needs to be associated with an existing Consumer object. To create a Consumer, use the [Consumer section](../../admin-gui/#consumers).

Create a consumer using the Kong Admin API.

```
$ curl -X POST \
    http://<kong_hostname>:8001/consumers \
    -H 'Content-Type: application/json' \
    -d '{
  	"username": "<kong_consumer_name>",
  	"custom_id": "<gluu_client_id>"
  }'
```

### Security & Access Proxy

To access a proxy upstream API, pass an UMA RPT token in the authorization header. Generate the UMA RPT token using OP Client credentials by sending a request to the oxd APIs.

For example, to access a Kong proxy using an UMA token:

```
curl -X GET \
  http://<kong_hostname>:8000/{path matching a configured Route} \
  -H 'Authorization: Bearer <uma_rpt_token>' \
```

!!! Note
    Kong normally provides the 8443 port for https by default, but during the setup script installation, it is changed to 443.

## Upstream Headers

When a client has been authenticated, the plugin will append some headers to the request before proxying it to the upstream service to identify the consumer and the end user in the code:

1. **X-Consumer-ID**, the ID of the Consumer on Kong
1. **X-Consumer-Custom-ID**, the custom_id of the Consumer (if set)
1. **X-Consumer-Username**, the username of the Consumer (if set)
1. **X-OAUTH-Client-ID**, the authenticated client ID (only if the consumer is not an 'anonymous' consumer)
1. **X-RPT-Expiration**, the token expiration time, integer timestamp, measured in the number of seconds since January 1, 1970 UTC, indicating when this token will expire, as defined in JWT RFC7519. It is only returned if the consumer is not set to 'anonymous'.
1. **X-Anonymous-Consumer**, will be set to true when authentication fails, and the 'anonymous' consumer is set instead.

This information can be used to implement additional logic. For example, use the X-Consumer-ID value to query the Kong Admin API and retrieve more information about the Consumer.


# Gluu UMA PEP

It is used to add Client authentication with UMA scope security. This plugin enables the use of an external OpenID Provider for OAuth2 client authentication. It needs to connect to Gluu's `oxd` service, which is an OAuth2 client middleware service.

It will support two tokens.

   1. **Default Access Token**: Plugin will authenticate it using introspection.
   2. **Access Token as JWT**: Plugin will authenticate it using JWT verify. Currently plugin supports only 3 Algorithm **RS256**, **RS384** and **RS512**.

## Configuration

You can configure plugin on **Service**, **Route** and **Global**. There are several possibilities for plugin configuration with services and routes. [More Details](https://docs.konghq.com/0.14.x/admin-api/#precedence).

!!! Important
    During plugin configuration, **GG UI** provide facility to create new OP Client when you set **oxd id blank**. But if you are using **Kong Admin API** then you need to use existing client credentials.

!!! Important
    konga.log shows the equivalent curl command that is all the request to Kong API and OXD API made by Konga GUI. You can use this curl command for automate the configuration instead of using the web interface.

### Enable plugin on Service

#### 1. Add Service

##### 1.1 Add Service using GG UI

Use [Service section](../admin-gui/#add-service) to add service using GG UI.

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

Use the [Manage Service](../admin-gui/#332-add-plugin) section in GG UI to enable the Gluu UMA PEP plugin. In the security category, there is a Gluu UMA PEP box. Click on the **+ icon** to enable the plugin.

![11_path_uma_service](../img/11_path_uma_service.png)

After clicking on **+ icon**, you will see the below form.
![11_path_add_uma_service](../img/11_path_add_uma_service.png)

##### 2.2 Configure plugin using Kong Admin API

```
$ curl -X POST \
  http://localhost:8001/plugins \
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
            ],
            "scope_expression": {
              "rule": {
                "and": [
                  {
                    "var": 0
                  },
                  {
                    "var": 1
                  }
                ]
              },
              "data": [
                "admin",
                "employee"
              ]
            }
          }
        ]
      }
    ],
    "ignore_scope": <false|true>,
    "deny_by_default": <false|true>,
    "hide_credentials": <false|true>
  },
  "service_id": "<kong_service_object_id>"
}'
```

!!! Note
    Kong not allow to proxy only using service object. you must have to add route for this. In short, you need one service to register Upstream API and one route for proxy.

### Enable plugin on Route

#### 1. Add Route

##### 1.1 Add Route using GG UI

Use [Manage Service Section](../admin-gui/#routes) to add route using GG UI.

![3_4_service_route](../img/3_4_service_route.png)

##### 1.2 Add Route using Kong Admin API

```
$ curl -X POST \
    http://localhost:8001/routes \
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
    What to put in hosts? It is one technique to request to proxy. There are several possibilities. Take a look on [Proxt reference](https://docs.konghq.com/0.14.x/proxy/) Kong Documents.

#### 2. Configure Plugin

##### 2.1 Configure plugin using GG UI

Use the [Manage Service](../admin-gui/#332-add-plugin) section in GG UI to enable the Gluu UMA PEP plugin. In the security category, there is a Gluu UMA PEP box. Click on the **+ icon** to enable the plugin.

![11_path_uma_service](../img/12_path_uma_route.png)

After clicking on **+ icon**, you will see the below form.
![11_path_add_uma_service](../img/12_path_add_uma_route.png)

##### 2.2 Configure plugin using Kong Admin API

```
$ curl -X POST \
  http://localhost:8001/plugins \
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
            ],
            "scope_expression": {
              "rule": {
                "and": [
                  {
                    "var": 0
                  },
                  {
                    "var": 1
                  }
                ]
              },
              "data": [
                "admin",
                "employee"
              ]
            }
          }
        ]
      }
    ],
    "ignore_scope": <false|true>,
    "deny_by_default": <false|true>,
    "hide_credentials": <false|true>
  },
  "route_id": "<kong_route_object_id>"
}'
```

### Enable plugin Globally

If you enable plugin globally, it will apply for all the services and routes.

#### 1. Configure Plugin

##### 1.1 Configure plugin using GG UI

Use the [Plugin section](../admin-gui/#add-plugin) in GG UI to enable the Gluu UMA PEP plugin. In the security category, there is a Gluu UMA PEP box. Click on the **+ icon** to enable the plugin.

![5_plugins_add](../img/5_plugins_add.png)

After clicking on **+ icon**, you will see the below form.
![11_path_add_uma_service](../img/12_path_add_uma_route.png)

##### 1.2 Configure plugin using Kong Admin API

```
$ curl -X POST \
  http://localhost:8001/plugins \
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
            ],
            "scope_expression": {
              "rule": {
                "and": [
                  {
                    "var": 0
                  },
                  {
                    "var": 1
                  }
                ]
              },
              "data": [
                "admin",
                "employee"
              ]
            }
          }
        ]
      }
    ],
    "ignore_scope": <false|true>,
    "deny_by_default": <false|true>,
    "hide_credentials": <false|true>
  }
}'
```

### Parameters

Here is a list of all the parameters which can be used in this plugin's configuration.

| field | Default | Description |
|-------|---------|-------------|
|**op_url**||The URL of you OP server. Example: https://op.server.com|
|**oxd_url**||The URL of you OXD server. Example: https://oxd.server.com|
|**oxd_id**||It is used to introspect the token.|
|**client_id**|| It is used to get protection access token to access introspect API. If you enter oxd id, you also need to enter client id and client secret of existing oxd client.|
|**client_secret**||It is used to get protection access token to access introspect API. If you enter oxd id, you also need to enter client id and client secret of existing oxd client.|
|**uma_scope_expression**||It is used to add scope security on UMA scope token.|
|**ignore_scope**| false |It will not check any token scope during authentication time.|
|**deny_by_default**| true |This functionality is for the path which is not protected by UMA scope expression. If it is true then deny unprotected path otherwise allow.|
|**anonymous**||An optional string (consumer uuid) value to use as an “anonymous” consumer if authentication fails. If empty (default), the request will fail with an authentication failure 4xx. Please note that this value must refer to the Consumer id attribute which is internal to Kong, and not its custom_id.|
|**hide_credentials**|false|An optional boolean value telling the plugin to show or hide the credential from the upstream service. If true, the plugin will strip the credential from the request (i.e. the Authorization header) before proxying it.|

!!! Note
    GG UI provide facility to create client and register resources using OXD. If you are configuring plugin only using Kong Admin API then you need to create client and register resource using OXD APIs and pass same UMA Expression and client credential to Gluu-UMA-PEP plugin.

#### UMA Scope Expression

UMA Scope Expression is a JSON expression, Used to register the resources in resource server. [More Details](https://gluu.org/docs/ce/3.1.4/api-guide/uma-api/#uma-permission-registration-api)

!!! Note
    You can enable and disable the UMA scope expression by setting `ignore_scope` in edit mode.

Let's say you have an API which you would like to protect:

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

At the runtime, it request to RS with RPT token and check the permission for requested resources.

#### Dynamic resource protection

If you want to protect a dynamic resource with UMA or OAuth scopes, you can do this by securing the parent path. For example, if you want to secure both `/folder` and `/folder/[id]`, you only need to secure `/folder` with a chosen scope. The protection of the parent will be applied to its children, unless different protection is explicitly defined.

Use cases for different resource security:

- Rule1 for path GET /root                  `{scope: a and b}`

- Rule2 for path GET /root/folder1          `{scope: c}`

- Rule3 for path GET /root/folder1/folder2  `{scope: d}`

```
GET /root                                  --> Apply Rule1
GET /root/1                                --> Apply Rule1
GET /root/one                              --> Apply Rule1
GET /root/one/two                          --> Apply Rule1
GET /root/two?id=df4edfdf                  --> Apply Rule1

GET /root/folder1                          --> Apply Rule2
GET /root/folder1/1                        --> Apply Rule2
GET /root/folder1?id=dfdf454gtfg           --> Apply Rule2
GET /root/folder1/one/two                  --> Apply Rule2
GET /root/folder1/one/two/treww?id=w4354f  --> Apply Rule2

GET /root/folder1/folder2/1                --> Apply Rule3
GET /root/folder1/folder2/one/two          --> Apply Rule3
GET /root/folder1/folder2/dsd545df         --> Apply Rule3
GET /root/folder1/folder2/one?id=fdfdf     --> Apply Rule3
```

## Usage

### Create Client

Create a client using [Create client consumer section](../admin-gui/#create-client). You can use OXD register-site API to create client.

### Create Consumer

You need to associate a client credential to an existing Consumer object. To create a Consumer use [Consumer section](../admin-gui/#consumers).

Create consumer using Kong Admin API.

```
$ curl -X POST \
    http://localhost:8001/consumers \
    -H 'Content-Type: application/json' \
    -d '{
  	"username": "<kong_consumer_name>",
  	"custom_id": "<gluu_client_id>"
  }'
```

### Security & Access Proxy

You need to pass UMA RPT token in authorization header to access proxy upstream API. You can generate the UMA RPT token using OP Client credentials by requesting to OXD APIs.

Example for access kong proxy using UMA token:

```
curl -X GET \
  http://localhost:8000/{path matching a configured Route} \
  -H 'Authorization: Bearer <uma_rpt_token>' \
```

!!! Note
    Kong provides the 8443 port for https by default, but during the setup script installation, we change it to 443.

## Upstream Headers

When a client has been authenticated, the plugin will append some headers to the request before proxying it to the upstream service, so that you can identify the consumer and the end user in your code:

1. **X-Consumer-ID**, the ID of the Consumer on Kong
1. **X-Consumer-Custom-ID**, the custom_id of the Consumer (if set)
1. **X-Consumer-Username**, the username of the Consumer (if set)
1. **X-OAuth-Client-ID**, the authenticated client id (only if the consumer is not an 'anonymous' consumer)
1. **X-OAuth-Expiration**, the token expiration time, integer timestamp, measured in the number of seconds since January 1, 1970 UTC, indicating when this token will expire, as defined in JWT RFC7519. It is only returned in oauth_mode (only if the consumer is not an 'anonymous' consumer)
1. **X-Anonymous-Consumer**, will be set to true when authentication fails, and the 'anonymous' consumer is set instead.

You can use this information on your side to implement additional logic. You can use the X-Consumer-ID value to query the Kong Admin API and retrieve more information about the Consumer.


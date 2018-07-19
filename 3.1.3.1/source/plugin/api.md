# Admin API

You can configure the plugins without the Admin GUI. There are two plugins.

!!! Note
    Configure the plugins using the [Admin GUI](./gui.md)

1. [Gluu OAuth 2.0 client credential authentication](#gluu-oauth-20-client-credential-authentication)
2. [Gluu OAuth 2.0 UMA RS plugin](#gluu-oauth-20-uma-rs-plugin)

## Gluu OAuth 2.0 client credential authentication

This plugin enables the use of an external OpenId Provider for OAuth2 client registration and authentication. It needs to connect via `https` to Gluu's `oxd-https-extension` service, which is an OAuth2 client middleware service. It provides OAuth 2.0 client credential authentication with three different modes.

### Terminology
* `api`: your upstream service placed behind Kong, which Kong proxies requests to.
* `plugin`: a plugin executing actions inside Kong before or after a request has been proxied to the upstream API.
* `consumer`: a developer or service using the API. When using Kong, a Consumer only communicates with Kong, which proxies every call to the said upstream API.
* `credentials`: in the gluu-oauth2-client-auth plugin context, an openId client is registered with consumer and client id is used to identify the credentials.

### Installation
1. [Install Kong](https://getkong.org/install/)
2. [Install oxd server v3.1.3](https://oxd.gluu.org/docs/)
3. Install gluu-oauth2-client-auth:
    1. Stop Kong : `kong stop`
    2. Using luarocks `luarocks install gluu-oauth2-client-auth`
       
        **or**
       
        Copy the `gluu-oauth2-client-auth/kong/plugins/gluu-oauth2-client-auth` Lua sources to the Kong plugins folder -- `/usr/local/share/lua/<version>/kong/plugins/gluu-oauth2-client-auth`
        
    3. Enable the plugin in your `kong.conf` (typically located at `/etc/kong/kong.conf`) and start Kong with `kong start`.
    
        ```
            custom_plugins = gluu-oauth2-client-auth
        ```

### Configuration
#### Add an API

The first step is to add your API in Kong. In order to do that, use the following request: 

```
$ curl -X POST http://localhost:8001/apis \
      --data "name=example" \
      --data "hosts=your.api.server" \
      --data "upstream_url=http://your.api.server.com"
```

Validate that your API is correctly proxied via Kong:

```
$ curl -i -X GET \
  --url http://localhost:8000/your/api \
  --header 'Host: your_api_server'
```

#### Enable the gluu-oauth2-client-auth protection

```
curl -X POST http://kong:8001/apis/{api}/plugins \
    --data "name=gluu-oauth2-client-auth" \
    --data "config.hide_credentials=true" \
    --data "config.op_server=<op_server.com>" \
    --data "config.oxd_http_url=<oxd_http_url>" \
    --data "config.oxd_id=<oxd_id>" \
    --data "config.anonymous=<consumer_id>"
```

**api**: The `id` or `name` of the API which this plugin configuration will target

| FORM PARAMETER | DEFAULT | DESCRIPTION |
|----------------|---------|-------------|
| name | | The name of the plugin to use, in this case: gluu-oauth2-client-auth. |
| config.hide_credentials (optional) | false | An optional boolean value telling the plugin to hide the credential to the upstream API server. It will be removed by Kong before proxying the request. |
| config.op_server | | OP server |
| config.oxd_http_url | | OXD HTTP extension URL |
| config.oxd_id (optional) | | Used to introspect the token. You can use any other oxd_id. If you do not pass it, the plugin creates a new client itself. |
| config.anonymous (optional) | | An optional string (Consumer uuid) value to use as an "anonymous" Consumer in case  authentication fails. If empty (default), the request will fail with an authentication failure 4xx. Please note that this value must refer to the Consumer id attribute which is internal to Kong, and not its custom_id. |

### Usage

In order to use the plugin, you first need to create a Consumer to associate one or more credentials with. The Consumer represents a developer using the final service/API.

#### Create a Consumer

You need to associate a credential to an existing Consumer object which represents a user consuming the API. To create a Consumer, you can execute the following request:

```
$ curl -X POST http://localhost:8001/consumers/ \
    --data "username=<USERNAME>" \
    --data "custom_id=<CUSTOM_ID>"

HTTP/1.1 201 Created
{
    "username":"<USERNAME>",
    "custom_id": "<CUSTOM_ID>",
    "created_at": 1472604384000,
    "id": "7f853474-7b70-439d-ad59-2481a0a9a904"
}
```

| PARAMETER | DEFAULT | DESCRIPTION |
|----------------|---------|-------------|
| username (semi-optional) | | The username of the Consumer. Either this field or `custom_id` must be specified. |
| custom_id (semi-optional) | | A custom identifier used to map the Consumer to another database. Either this field or `username` must be specified. |

#### Create an OAuth credential

This process registers an OpenId client with oxd which helps you get tokens and authenticate the token. The plugin behaves as per selected mode. There are three modes.

| Mode | DESCRIPTION |
|----------------|-------------|
| oauth_mode | If set to Yes, the client must present an `ACTIVE` OAuth token to call an API. |
| uma_mode | If set to Yes, the client must present an `ACTIVE` UMA RPT token to call an  API. You need to configure the [gluu-oauth2-rs](https://github.com/GluuFederation/gluu-gateway/tree/master/gluu-oauth2-rs) plugin for uma_mode. |
| mix_mode | If set to Yes, the client must present an `ACTIVE` OAuth token to call an API. Kong will obtain an UMA permission ticket, and attempt to obtain an RPT on behalf of the client. The client can send pushed claims using the `UMA_PUSHED_CLAIMS` header with JSON in the following format: `{"claim_token":"...","claim_token_format":"..."}`. You need to configure the [gluu-oauth2-rs](https://github.com/GluuFederation/gluu-gateway/tree/master/gluu-oauth2-rs) plugin for mix_mode. |

You can provision new credentials by making the following HTTP request:

```
curl -X POST \
  http://localhost:8001/consumers/{consumer}/gluu-oauth2-client-auth/ \
  -d name=<name>
  -d op_host=<op_host>
  -d oxd_http_url=<oxd_http_url>
  -d oauth_mode=<true|false>
  -d uma_mode=<true|false>
  -d mix_mode=<true|false>
  -d oxd_id=<existing_oxd_id>
  -d client_name=<client_name>
  -d client_id=<existing_client_id>
  -d client_secret=<existing_client_secret>
  -d allow_unprotected_path=<true|false>
  -d client_jwks_uri=<client_jwks_uri>
  -d client_token_endpoint_auth_method=<client_token_endpoint_auth_method>
  -d client_token_endpoint_auth_signing_alg=<client_token_endpoint_auth_signing_alg>

RESPONSE :
{
  "id": "e1b1e30d-94fa-4764-835d-4fae0f8ff668",
  "created_at": 1517216795000,
  "consumer_id": "81ae39fa-d08e-4978-a6af-be0127b9fb99"
  "name": <name>,
  "op_host": <op_host>,
  "oxd_http_url": <oxd_http_url>,
  "oauth_mode": <true|false>,
  "uma_mode": <true|false>,
  "mix_mode": <true|false>,
  "oxd_id": <oxd_id>,
  "client_name": <client_name>,
  "client_id": <client_id>,
  "client_secret": <client_secret>,
  "client_id_of_oxd_id": <client_id_of_oxd_id>,
  "allow_unprotected_path": <true|false>,
  "client_jwks_uri": <client_jwks_uri>,
  "client_token_endpoint_auth_method": <client_token_endpoint_auth_method>
  "client_token_endpoint_auth_signing_alg": <client_token_endpoint_auth_signing_alg>
}
```

| FORM PARAMETER | DEFAULT | DESCRIPTION |
|----------------|---------|-------------|
| name | | The name to associate with the credential. In OAuth 2.0, this would be the application name. |
| op_host | | An OpenId connect provider. Example: https://gluu.example.org |
| oxd_http_url | | An OXD https extension url. |
| oauth_mode (semi-optional) | | If set to Yes, Kong acts as an OAuth client only. |
| uma_mode (semi-optional) | | This indicates your client is a valid UMA client, and obtains and sends an RPT as the access token. |
| mix_mode (semi-optional) | | If set to Yes, then the gluu-oauth2 plugin will try to obtain an UMA RPT token if the RS returns  401/Unauthorized. |
| allow_unprotected_path (optional) | false | It is used to allow or deny an unprotected path by UMA-RS. |
| allow_oauth_scope_expression (optional) | false | If set to Allow, an OAuth scope expression will be applied on the scope of the token in the oauth mode. |
| restrict_api (optional) | false | The client can only call specified APIs if client restriction is enabled. |
| restrict_api_list (optional) | | A string of comma-separated api_ids. You have to enable `restrict_api` to use this feature. |
| show_consumer_custom_id (optional) | true | If true, then the plugin will set a consumer custom id in the legacy header.|
| oxd_id (optional) | | If you have an existing oxd entry, enter the oxd_id(also client id, client secret and client id of oxd id). If you have a client created from the OP server, skip it and enter only the client_id and client_secret below. If you skip all the five fields (oxd_id, setup_client_oxd_id, client_id, client_secret and  client_id_of_oxd_id), then it will create a new client for you. |
| client_name (optional) | kong_oauth2_bc_client | An optional string value for the client name. |
| client_id (optional) | | You can use an existing client id. |
| client_secret (optional) | | You can use an existing client secret. |
| client_id_of_oxd_id (optional) | | You can use an existing client id of oxd id. |
| setup_client_oxd_id (optional) | | If you have an existing oxd id, add value in setup client oxd Id. |
| allow_unprotected_path (false) | | It is used to allow or deny an unprotected path by UMA-RS. |
| client_jwks_uri (optional) | | An optional string value for a client jwks uri. |
| client_token_endpoint_auth_method (optional) | | An optional string value for the client token endpoint auth method. |
| client_token_endpoint_auth_signing_alg (optional) | | An optional string value for the client token endpoint auth signing alg. |

The next step is to configure the [Gluu OAuth 2.0 UMA RS plugin](#gluu-oauth-20-uma-rs-plugin).

## Gluu OAuth 2.0 UMA RS plugin

It is a User-Managed Access Resource Server plugin which allows you to protect your API (proxied by Kong) with [UMA](https://docs.kantarainitiative.org/uma/rec-uma-core.html).

!!! Note
    You need to configure the **gluu-oauth2-client-auth** plugin first.

### Installation

1. [Install Kong](https://getkong.org/install/)
2. [Install oxd server v3.1.3](https://oxd.gluu.org/docs/)
3. Install gluu-oauth2-rs
    1. Stop Kong : `kong stop`
    2. 
        Using luarocks `luarocks install gluu-oauth2-rs`
        
        **or**
        
        Copy the `gluu-oauth2-rs/kong/plugins/gluu-oauth2-rs` Lua sources to the Kong plugins folder `kong/plugins/gluu-oauth2-rs`        
            
    3. Enable the plugin in your `kong.conf` (typically located at `/etc/kong/kong.conf`) and start Kong `kong start`.

        ```
            custom_plugins = gluu-oauth2-rs
        ```

### Configuration

 - oxd_host - OPTIONAL, the host of the oxd server (default: localhost. It is recommended to have the oxd server on localhost.)
 - protection_document - REQUIRED, a JSON document that describes UMA protection
 - uma_server_host - REQUIRED, an UMA Server which implements UMA 2.0 specification.
                     (For example [Gluu Server](https://www.gluu.org/gluu-server/overview/)). 
                     Make sure that the UMA implementation is up and running by visiting the `.well-known/uma2-configuration` endpoint.
 - oauth_scope_expression - OAuth Scope Expression is a JSON expression which defines security for OAuth scopes. It checks the scope (from token introspection) of the token with the configured OAuth JSON expression. 
               
#### Protection document   

Protection document - a JSON document which describes UMA protection in a declarative way and is based on the [uma-rs](https://github.com/GluuFederation/uma-rs) project.

 - path - a relative path to [protect](#dynamic-resource-protection)
 - httpMethods - GET, HEAD, POST, PUT, DELETE
 - scope - the scope required to access the given path
 - ticketScopes - an optional parameter which may be used to keep the ticket scope as narrow as possible. If not specified, the plugin will register the ticket with its scopes specified by "scope," which may often  be unwanted. (For example, the scope may have "http://photoz.example.com/dev/actions/all" and the authorized ticket may grant access also to other resources).
    
Let's say you have APIs which you would like to protect:

 - GET https://your.api.server.com/photo  (UMA scope: http://photoz.example.com/dev/actions/view)
 - PUT https://your.api.server.com/photo  (UMA scope: http://photoz.example.com/dev/actions/all or http://photoz.example.com/dev/actions/add)
 - POST https://your.api.server.com/photo  (UMA scope: http://photoz.example.com/dev/actions/all or http://photoz.example.com/dev/actions/add)
 - GET https://your.api.server.com/document  (UMA scope: http://photoz.example.com/dev/actions/view)

A protection document for this sample (upstream_url=http://your.api.server.com/, request_host=your.api.server.com for Kong add API):

```
[
    {
        "path":"/photo",
        "conditions":[
            {
                "httpMethods":["GET"],
                "scopes":[
                    "http://photoz.example.com/dev/actions/view"
                ]
            },
            {
                "httpMethods":["PUT", "POST"],
                "scopes":[
                    "http://photoz.example.com/dev/actions/all",
                    "http://photoz.example.com/dev/actions/add"
                ],
                "ticketScopes":[
                    "http://photoz.example.com/dev/actions/add"
                ]
            }
        ]
    },
    {
        "path":"/document",
        "conditions":[
            {
                "httpMethods":["GET"],
                "scopes":[
                    "http://photoz.example.com/dev/actions/view"
                ]
            }
        ]
    }
]
```

You can also pass the scope-expression format.

```
[
  {
    "path": "/photo",
    "conditions": [
      {
        "httpMethods": [
          "GET"
        ],
        "scope_expression": {
          "rule": {
            "or": [
              {
                "var": 0
              }
            ]
          },
          "data": [
            "http://photoz.example.com/dev/actions/view"
          ]
        }
      },
      {
        "httpMethods": [
          "PUT",
          "POST"
        ],
        "scope_expression": {
          "rule": {
            "or": [
              {
                "var": 0
              },
              {
                "var": 1
              }
            ]
          },
          "data": [
            "http://photoz.example.com/dev/actions/all",
            "http://photoz.example.com/dev/actions/add"
          ]
        },
        "ticketScopes": [
          "http://photoz.example.com/dev/actions/add"
        ]
      }
    ]
  },
  {
    "path": "/document",
    "conditions": [
      {
        "httpMethods": [
          "GET"
        ],
        "scope_expression": {
          "rule": {
            "or": [
              {
                "var": 0
              }
            ]
          },
          "data": [
            "http://photoz.example.com/dev/actions/view"
          ]
        }
      }
    ]
  }
]
```

#### Dynamic resource protection

If you want to protect a dynamic resource with UMA or OAuth scopes, you can do this by securing the parent path. For example, if you want to secure both `/folder` and `/folder/[id]`, you only need to secure `/folder` with a chosen scope. The protection of the parent will be applied to its children, unless different protection is explicitly defined.

Use cases for different resource security:
- Rule1 for path GET /root                  `{scope: a and b}`
- Rule2 for path GET /root/folder1          `{scope: c}`
- Rule3 for path GET /root/folder1/folder2  `{scope: d}`

```
GET /root     --> Apply Rule1
GET /root/1    --> Apply Rule1
GET /root/one    --> Apply Rule1
GET /root/one/two  --> Apply Rule1
GET /root/two?id=df4edfdf  --> Apply Rule1

GET /root/folder1   --> Apply Rule2
GET /root/folder1/1   --> Apply Rule2
GET /root/folder1?id=dfdf454gtfg  --> Apply Rule2
GET /root/folder1/one/two  --> Apply Rule2
GET /root/folder1/one/two/treww?id=w4354f  --> Apply Rule2

GET /root/folder1/folder2/1    --> Apply Rule3
GET /root/folder1/folder2/one/two  --> Apply Rule3
GET /root/folder1/folder2/dsd545df   --> Apply Rule3
GET /root/folder1/folder2/one?id=fdfdf  --> Apply Rule3
```

#### OAuth Scope Expression

OAuth Scope Expression is a JSON expression, security for OAuth scopes. It checks the scope (from token introspection) of the token with the configured OAuth JSON expression. 

!!! Note
    You can enable and disable the OAuth scope expression by using [OAuth credential's `OAuth scope security`](#create-an-oauth-credential) flag.

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
          "and": [
            "openid",
            {
              "or": [
                "email",
                "clientinfo"
              ]
            }
          ]
        }
      }
    ]
  }
]
```

In the runtime, it matches the scope expression with token scopes. The inner expression is executed first. It takes the scopes from the expression one by one and matches them with the requested scope. If it exists, 'true' is returned. If not, it is 'false'.

1. Let's assume a token with the `["clientinfo"]` scope only.
    - `"email"` or `"clientinfo"` = `false` or `true` = `true`
    - `"openid"` and `true` = `false` and `true` = `false`
    - The result is `false`, so the request is not allowed.

2. Let's assume a token with `["openid", "clientinfo"]` scopes.
    - `"email"` or `"clientinfo"` = `false` or `true` = `true`
    - `"openid"` and `true` = `true` and `true` = `true`
    - The result is `true`, so the request is allowed.


### Protect your API with UMA

#### Add your API server to kong /apis

```curl
$ curl -i -X POST \
  --url http://localhost:8001/apis/ \
  --data 'name=your.api.server' \
  --data 'upstream_url=http://your.api.server.com/' \
  --data 'request_host=your.api.server.com'
```

The response must confirm the API has been added:

```
HTTP/1.1 201 Created
Content-Type: application/json
Connection: keep-alive

{
  "request_host": "your.api.server.com",
  "upstream_url": "http://your.api.server.com/",
  "id": "2eec1cb2-7093-411a-c14e-42e67142d2c4",
  "created_at": 1428456369000,
  "name": "your.api.server"
}
```

Validate that your API is correctly proxied via Kong:

```
$ curl -i -X GET \
  --url http://localhost:8000/your/api \
  --header 'Host: your.api.server.com'
```

#### Enable gluu-oauth2-rs protection

Important : each protection_document and oauth_scope_expression double quotes must be escaped by the '\\' sign. This limitation comes from the Kong configuration parameter type limitation which is limited to: "id", "number", "boolean", "string", "table", "array", "url", "timestamp".
   
During gluu-oauth2-rs addition to /plugins, keep in mind that oxd must be up and running; otherwise, the registration will fail. It's because during a POST call to Kong's /plugin endpoint, the plugin performs self-registration on the oxd server at oxd_host provided in the configuration. For this reason, if the plugin is added and you remove oxd (or install a new version of oxd) without configuration persistence, then gluu-oauth2-rs must be re-registered (to force registration with the newly installed oxd).
    

```
$ curl -i -X POST \
  --url http://localhost:8001/apis/2eec1cb2-7093-411a-c14e-42e67142d2c4/plugins/ \
  --data "name=gluu-oauth2-rs" \
  --data "config.oxd_host=localhost" \
  --data "config.uma_server_host=https://uma.server.com" \
  --data "config.protection_document=[  
                                        {  
                                           \"path\":\"/photo\",
                                           \"conditions\":[  
                                              {  
                                                 \"httpMethods\":[  
                                                    \"GET\"
                                                 ],
                                                 \"scopes\":[  
                                                    \"http://photoz.example.com/dev/actions/view\"
                                                 ]
                                              },
                                              {  
                                                 \"httpMethods\":[  
                                                    \"PUT\",
                                                    \"POST\"
                                                 ],
                                                 \"scopes\":[  
                                                    \"http://photoz.example.com/dev/actions/all\",
                                                    \"http://photoz.example.com/dev/actions/add\"
                                                 ],
                                                 \"ticketScopes\":[  
                                                    \"http://photoz.example.com/dev/actions/add\"
                                                 ]
                                              }
                                           ]
                                        },
                                        {  
                                           \"path\":\"/document\",
                                           \"conditions\":[  
                                              {  
                                                 \"httpMethods\":[  
                                                    \"GET\"
                                                 ],
                                                 \"scopes\":[  
                                                    \"http://photoz.example.com/dev/actions/view\"
                                                 ]
                                              }
                                           ]
                                        }
                                     ]\"
```

The next step is to access and verify your API using the Kong proxy endpoint. 

!!! Note
    If you have access to the used oxTrust admin UI, you can manage your users and their authentication following [this documentation](https://gluu.org/docs/ce/admin-guide/oxtrust-ui/).

## Verify your API

After the configuration, you are ready to verify whether your API is protected by plugins or not. You need to pass the token as per configured [authentication mode](#create-an-oauth-credential).

A sample request to the proxy endpoint is given below. You can configure the port for the proxy endpoint using [kong config](../configuration.md#kong).

By default, Kong provides two endpoints.

| Protocol | Proxy endpoints |
|----------|-----------------|
| https | https://your.gg.host.com:8443 |
| http | http://your.gg.host.com:8000 |

!!! Note
    Kong provides the 8443 port for https by default, but during the setup script installation, we change it to 443.

```
$ curl -X GET \
    http://your.gg.server.com:8000/your_api_endpoint \
    -H 'authorization: Bearer 481aa800-5282-4d6c-8001-7dcdf37031eb' \
    -H 'host: your.api.server.com'
```

### 401/Unauthorized 

The call returns 401/Unauthorized when your token is invalid or expired.

```
HTTP/1.1 401 Unauthorized
{
    "message": "Unauthorized"
}
```

### 403/Forbidden

The call returns 403/Forbidden when you did not specify the required authorized RPT in the "Authorization" header. Also, you will get the `WWW-Authenticate` header with `ticket`.

```
HTTP/1.1 403 Forbidden
WWW-Authenticate: UMA realm="rs",
  as_uri="https://uma.server.com",
  error="insufficient_scope",
  ticket="016f84e8-f9b9-11e0-bd6f-0021cc6004de"

{
    "message": "Unauthorized"
}
```
You can make an uma-rp-get-rpt call to the oxd server, passing the obtained ticket with a consumer access token to obtain an RPT. Then you can use it to access an API. To learn more about the request, follow the [oxd documentation.](https://gluu.org/docs/oxd/3.1.3.1/api/#uma-rp-get-rpt)

### 200 Success

If your token is valid, you will get a success response from your upstream API.

## Upstream Headers

When a client has been authenticated, the plugin will append some headers to the request before proxying it to the upstream service, so that you can identify the consumer and the end user in your code:

1. **X-Consumer-ID**, the ID of the Consumer on Kong
2. **X-Consumer-Custom-ID**, the custom_id of the Consumer (if set)
3. **X-Consumer-Username**, the username of the Consumer (if set)
4. **X-Authenticated-Scope**, the comma-separated list of scopes that the end user has authenticated, if available (only if the consumer is not an 'anonymous' consumer)
5. **X-OAuth-Client-ID**, the authenticated client id, if oauth_mode is enabled (only if the consumer is not an 'anonymous' consumer)
6. **X-OAuth-Expiration**, the token expiration time, integer timestamp, measured in the number of seconds since January 1, 1970 UTC, indicating when this token will expire, as defined in JWT RFC7519. It is only returned in oauth_mode (only if the consumer is not an 'anonymous' consumer)
7. **X-Anonymous-Consumer**, will be set to true when authentication fails, and the 'anonymous' consumer is set instead.

You can use this information on your side to implement additional logic. You can use the X-Consumer-ID value to query the Kong Admin API and retrieve more information about the Consumer.

## Application creation in oxd

An application, as presented in the oxd ecommerce platform, is an oxd client created in the Gluu Gateway and actively used to make a call to the oxd-server. Each setup of the Gluu Gateway will require multiple clients. The following actions result in the creation of active clients which you can always inspect in the oxd ecommerce platform:

- installation, setup and logging in to the Gluu Gateway = 2 applications (2 active clients used)
- creation of an API protected with `gluu-oauth2-rs` = 2 applications (2 active clients used)
- an `Oauth flow` = 1 application (1 active client used) 
- an `UMA flow` = 2 applications (2 active clients used)
- a `Mix flow` = 1 application (1 active client used)

Note!!!
    A flow is an end-to-end sequence of calls to get the token necessary to make a successful call for a protected resource, used in any of the three modes of access management provided by the Gluu Gateway. You can test the three modes and their basic flows using the prepared `Katalon` tests available [here]( https://github.com/GluuFederation/gluu-gateway/tree/master/tests), as well as the `REST Postman` collections available in [this repo]( https://github.com/GluuFederation/gluu-gateway/tree/master/postman).
    
These actions do not result in the creation of active clients and therefore are not shown in the oxd ecommerce platform:

- creation of unprotected APIs = 0 applications
- creation of an API protected with `gluu-oauth2-client-auth` = 0 applications
- creation of Consumers = 0 applications
- creation of Consumers protected with `gluu-oauth2-client-auth` = 0 applications

Remember that Gluu Gateway uses oxd OAuth 2.0 client software to leverage the Gluu Server for client credentials and policy enforcement. oxd is commercial software, priced $10 per OAuth client per month.
The first 10 oxd clients are always free, and there is a five (5) day grace period for each new client–meaning: only clients active for 5 or more days are recorded for billing purposes.

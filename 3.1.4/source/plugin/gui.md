# Admin GUI

You can configure the plugins using the Admin GUI - Konga. There are two plugins. 

!!! Note
    Configure the plugins using the [Admin API](./api.md)
    
1. [Gluu OAuth 2.0 client credential authentication](#gluu-oauth-20-client-credential-authentication)
2. [Gluu OAuth 2.0 UMA RS plugin](#gluu-oauth-20-uma-rs-plugin)


## Gluu OAuth 2.0 client credential authentication

This plugin enables the use of an external OpenId Provider for OAuth2 client registration and authentication. It needs to connect via `https` to Gluu's `oxd-https-extension` service, which is an OAuth2 client middleware service. It provides OAuth 2.0 client credential authentication with [three different modes.](/#oauth-mode)

### Add an API

The first step is to add an API in Kong. Use the [API Section](../admin-gui.md#apis) to add your API in Kong.

### Enable gluu-oauth2-client-auth protection

Use the [Manage APIs](../admin-gui.md#manage-apis) section to enable the `gluu-oauth2-client-auth` plugin. In the `custom` section, there is a `Gluu OAuth2 Client Auth` box. Click on the `+` icon to enable the plugin.

![add_plugin_api](../img/3_1_add_plugin_api.png)

| **FORM PARAMETER** | **DESCRIPTION** |
|---------------|-----------------|
| oxd id (optional) | Used to introspect the token. By default it takes oxd_id from [Config](../configuration.md#admin-gui-portal-konga). You can also enter any other oxd_id. If you leave `oxd_id` blank during `gluu-oauth2-client-auth` addition, it will register the client in oxd and generate an application in the oxd-ecommerce platform when the plugin uses it for token introspection. |
| config.client_id (optional) | Used to get protection access token for introspection api. You can use any other oxd_id. If you leave `oxd_id` blank during `gluu-oauth2-client-auth` addition, it will register the client in oxd and generate an application in the oxd-ecommerce platform when the plugin uses it for token introspection. |
| config.client_secret (optional) | Used to get protection access token for introspection api. You can use any other oxd_id. If you leave `oxd_id` blank during `gluu-oauth2-client-auth` addition, it will register the client in oxd and generate an application in the oxd-ecommerce platform when the plugin uses it for token introspection. |
| anonymous (optional) | An optional string (consumer uuid) value to use as an `anonymous` consumer if authentication fails. If empty (default), the request will fail with an authentication failure 4xx. Please note that this value must refer to the Consumer id attribute which is internal to Kong, and not its custom_id. |
| hide credentials (optional) | An optional boolean value telling the plugin to hide the credential to the upstream API server. It will be removed by Kong before proxying the request |

!!! Note
    See all the attribute descriptions in the [Admin API](api.md#enable-gluu-oauth2-client-auth-protection) section. 

In order to use the plugin, you first need to create a Consumer to associate one or more credentials with. The Consumer represents a developer using the final service/API.

### Create a Consumer 

You need to associate a credential with an existing Consumer object which represents a user consuming the API. To create a Consumer, you can use the [Consumer section](/admin-gui/#consumers).

### Create an OAuth credential

!!! Note
    Consumer creation only registers the client in oxd. It will generate an [application](#application-creation-in-oxd) when you make a token request.
    `UMA-RS` addition registers the client and resources, so it will create 2 applications.
    If you leave `oxd_id` blank during `gluu-oauth2-client-auth` addition, it will register the client in oxd and generate an application in the oxd-ecommerce platform when the plugin uses it for token introspection.
    
This process registers an OpenId client with oxd which helps you get tokens and authenticate the token. The Plugin behaves as per selected mode. There are three modes. 

| Mode | DESCRIPTION |
|----------------|-------------|
| oauth_mode | If set to Yes, the client must present an `ACTIVE` OAuth token to call an API. |
| uma_mode | If set to Yes, the client must present an `ACTIVE` UMA RPT token to call an  API. You need to configure the [gluu-oauth2-rs](/plugin/gui/#gluu-oauth-20-uma-rs-plugin) plugin for uma_mode. |
| mix_mode | If set to Yes, the client must present an `ACTIVE` OAuth token to call an API. Kong will obtain an UMA permission ticket, and attempt to obtain an RPT on behalf of the client. The client can send pushed claims using the `UMA_PUSHED_CLAIMS` header with JSON in the following format: `{"claim_token":"...","claim_token_format":"..."}`. You need to configure the [gluu-oauth2-rs](/plugin/gui/#gluu-oauth-20-uma-rs-plugin) plugin for mix_mode. |

Use the [Consumer credentials configuration](../admin-gui.md#consumer-credentials-configuration) section to create an OAuth credential. In the `Credentials` section, there is an `OAuth2` section. Click on the `+ Create credentials` button.

![consumer_credential_list](../img/4_1_consumer_credential_list.png)

Below you can see the OAuth credential creation form:

![consumer_credential_add](../img/4_2_consumer_credential_add.png)

| FORM PARAMETER | DESCRIPTION |
|----------------|-------------|
| name | The name to associate with the credential. In OAuth 2.0, this would be the application name. |
| OAuth mode (semi-optional) | If set to Yes, Kong acts as an OAuth client only. |
| UMA mode (semi-optional) | This indicates your client is a valid UMA client, and obtains and sends an RPT as the access token. |
| Mix mode (semi-optional) | If set to Yes, then the gluu-oauth2 plugin will try to obtain an UMA RPT token if the RS returns 401/Unauthorized. |
| Allow unprotected path (false) | It is used to allow or deny an unprotected path by UMA-RS. |
| OAuth scope security (false) | If true, OAuth scope expression will be applied on scope of token in oauth mode. |
| Restrict API's (false) | The client can only call specified API's if client restriction is enabled. You can choose list of APIs by using `+ SELECT RESTRICTED API` button. |
| Show Consumer custom Id (true) | If true, then the plugin will set consumer custom id in legacy header otherwise not. |
| oxd id (optional) | If you have an existing oxd entry, enter the oxd_id(also client id, client secret and client id of oxd id). If you have a client created from the OP server, skip it and enter only the client_id and client_secret below. If you skip all the five fields (oxd_id, setup_client_oxd_id, client_id, client_secret and  client_id_of_oxd_id), then it will create a new client for you. |
| client name (optional) | An optional string value for the client name. |
| client id (optional) | You can use an existing client id. |
| client secret (optional) | You can use an existing client secret. |
| client id of oxd id (optional) | If you have an existing oxd id, add value in client id of oxd id. |
| setup client oxd id(optional) | If you have an existing oxd id, add value in setup client oxd Id. |
| client id of oxd id (optional) | You can use an existing client id of oxd id. |
| client jwks uri (optional) | An optional string value for a client jwks uri. |
| client token endpoint auth method (optional) | An optional string value for the client token endpoint auth method. |
| client token endpoint auth signing_alg (optional) | An optional string value for the client token endpoint auth signing alg. |

!!! Note
    Remember to copy and save the created consumer credentials because there is no other way to recover the client secret.
    
The next step is to configure [Gluu OAuth 2.0 UMA RS plugin](#gluu-oauth-20-uma-rs-plugin).

## Gluu OAuth 2.0 UMA RS Plugin

It is a User-Managed Access Resource Server plugin which allows you to protect your API (proxied by Kong) with [UMA](https://docs.kantarainitiative.org/uma/rec-uma-core.html).

!!! Note
    You need to configure the **gluu-oauth2-client-auth** plugin first.


### Add an API

The first step is to add your API in Kong. Use the [API Section](../admin-gui.md#apis) to add it in Kong.

### Enable gluu-oauth2-rs protection

!!! Note
    `UMA-RS` addition registers the client and resources, so it will create 2 [applications](#application-creation-in-oxd).

Use the `SECURITY` link in the [API](../admin-gui.md#apis) section.

![add_uma_rs](../img/3_add_oauth2_rs.png)

!!! Note
    See all the attribute descriptions in the [Admin API](api.md#gluu-oauth-20-uma-rs-plugin) section. 

You can register your two types of resources using this section.
 
 1. [Protection document](#protection-document)
 2. [OAuth Scope Expression](#oauth-scope-expression)

#### Protection document

Protection document (UMA Resources) is a JSON document which describes UMA protection in a declarative way and is based on the [uma-rs](https://github.com/GluuFederation/uma-rs) project. Use `UMA Resources` button to open protection document section. 

![UMA Resources](../img/3_4_add_uma_rs.png)

 - path - a relative path to [protect](#dynamic-resource-protection)
 - httpMethods - GET, HEAD, POST, PUT, DELETE
 - scope - the scope required to access the given path
 - ticketScopes - an optional parameter which may be used to keep the ticket scope as narrow as possible. If not specified, the plugin will register the ticket with its scopes specified by "scope," which may often be unwanted. (For example, the scope may have "http://photoz.example.com/dev/actions/all" and the authorized ticket may grant access also to other resources).
    
Let's say you have APIs which you would like to protect:

 - GET https://your.api.server.com/photo  (UMA scope: http://photoz.example.com/dev/actions/view)
 - PUT https://your.api.server.com/photo  (UMA scope: http://photoz.example.com/dev/actions/all or http://photoz.example.com/dev/actions/add)
 - POST https://your.api.server.com/photo  (UMA scope: http://photoz.example.com/dev/actions/all or http://photoz.example.com/dev/actions/add)
 - GET https://your.api.server.com/document  (UMA scope: http://photoz.example.com/dev/actions/view)

A protection document for this sample:

![add_resources](../img/3_3_add_resource.png)

You can see the below JSON by clicking on the `VIEW RESOURCES JSON` button. Scope expression is defined as JsonLogic rule.

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

!!! Note
    If there is no authorization policy in your UMA scope then you need to set `umaGrantAccessIfNoPolicies` oxAuth property to `true` in your AS(authorization server). 

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

OAuth Scope Expression is a JSON expression which defines security for OAuth scopes. It checks the scope (from token introspection) of the token with the configured OAuth JSON expression. Use the `OAuth Scope Security` button to switch. 

!!! Note
    You can enable and disable OAuth scope expression by using the [OAuth credential's `OAuth scope security`](#create-an-oauth-credential) flag.

![OAuth_Expression](../img/3_4_add_oauth_scope_expression.png)

 - path - a relative path to [protect](#dynamic-resource-protection)
 - httpMethods - GET, HEAD, POST, PUT, DELETE
 - scope - the OAuth scope required to access the given path
 
Let's say you have an API which you would like to protect:

![Add_OAuth_Expression](../img/3_5_oauth_scopes.png)

Use the `VIEW RESOURCE JSON` button to see the JSON expression.

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
    - The result is `false`, so the request is not allowed

2. Let's assume a token with `["openid", "clientinfo"]` scopes.
    - `"email"` or `"clientinfo"` = `false` or `true` = `true`
    - `"openid"` and `true` = `true` and `true` = `true`
    - The result is `true`, so the request is allowed

The next step is to access and verify your API using the Kong proxy endpoint.

!!! Note
    If you have access to the used oxTrust admin UI, you can manage your users and their authentication following [this documentation](https://gluu.org/docs/ce/admin-guide/oxtrust-ui/).

## Verify you API

After the configuration, you are ready to verify whether your API is protected by plugins or not. You need to pass the token as per configured [authentication mode](#create-an-oauth-credential).

A sample request to the proxy endpoint is given below. You can configure the port for the proxy endpoint using [kong config](../configuration.md#kong).

| Protocol | Proxy endpoints |
|----------|-----------------|
| https | https://your.gg.host.com |
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
You can make an uma-rp-get-rpt call to the oxd server, passing the obtained ticket with a consumer access token to obtain an RPT. Then you can use it to access an API. To learn more about the request, follow the [oxd documentation.](https://gluu.org/docs/oxd/3.1.4/api/#uma-rp-get-rpt)

### 200 Success

If your token is valid, you will get a success response from your upstream API.

## Upstream Headers

When a client has been authenticated, the plugin will append some headers to the request before proxying it to the upstream service, so that you can identify the consumer and the end user in your code:

1. **X-Consumer-ID**, the ID of the Consumer on Kong
2. **X-Consumer-Custom-ID**, the custom_id of the Consumer (if set)
3. **X-Consumer-Username**, the username of the Consumer (if set)
4. **X-Authenticated-Scope**, the comma-separated list of scopes that the end user has authenticated, if available (only if the consumer is not an 'anonymous' consumer)
5. **X-OAuth-Client-ID**, the authenticated client id, if oauth_mode is enabled (only if the consumer is not an 'anonymous' consumer)
6. **X-OAuth-Expiration**, the token expiration time, Integer timestamp, measured in the number of seconds since January 1, 1970 UTC, indicating when this token will expire, as defined in JWT RFC7519. It is only returned in oauth_mode (only if the consumer is not an 'anonymous' consumer)
7. **X-Anonymous-Consumer**, will be set to true when authentication fails, and the 'anonymous' consumer is set instead.

You can use this information on your side to implement additional logic. You can use the X-Consumer-ID value to query the Kong Admin API and retrieve more information about the Consumer.

## Application creation in oxd

An application, as presented in the oxd ecommerce platform, is an oxd client created in the Gluu Gateway and actively used to make a call to the oxd-server. Each setup of the Gluu Gateway will require multiple clients. The following actions result in the creation of active clients which you can always inspect in the oxd ecommerce platform:

- installation, setup and logging in to the Gluu Gateway = 2 applications (2 active clients used)
- creation of an API protected with `gluu-oauth2-rs` = 2 applications (2 active clients used)
- an `Oauth flow` = 1 application* (1 active client used) 
- an `UMA flow` = 2 applications (2 active clients used)
- a `Mix flow` = 1 application* (1 active client used)

(*) When you choose to leave `oxd_id` blank in the `gluu-oauth2-client-auth` protection process, two applications are created.

!!! Note
    A flow is an end-to-end sequence of calls to get the token necessary to make a successful call for a protected resource, used in any of the three modes of access management provided by the Gluu Gateway. You can test the three modes and their basic flows using the prepared `Katalon` tests available [here]( https://github.com/GluuFederation/gluu-gateway/tree/master/tests), as well as the `REST Postman` collections available in [this repo]( https://github.com/GluuFederation/gluu-gateway/tree/master/postman).
    
These actions do not result in the creation of active clients and therefore are not shown in the oxd ecommerce platform:

- creation of unprotected APIs = 0 applications
- creation of an API protected with `gluu-oauth2-client-auth` = 0 applications
- creation of Consumers = 0 applications
- creation of Consumers protected with `gluu-oauth2-client-auth` = 0 applications

Remember that Gluu Gateway uses oxd OAuth 2.0 client software to leverage the Gluu Server for client credentials and policy enforcement. oxd is commercial software, priced $10 per OAuth client per month.
The first 10 oxd clients are always free, and there is a five (5) day grace period for each new client–meaning: only clients active for 5 or more days are recorded for billing purposes.

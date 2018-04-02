# Admin GUI

You can configure plugins using Admin GUI - Konga. There are two plugins. 

!!! Note
    Configure using [Admin API](./api.md)
    
1. [Gluu OAuth 2.0 client credential authentication](#gluu-oauth-20-client-credential-authentication)
2. [Gluu OAuth 2.0 UMA RS plugin](#gluu-oauth-20-uma-rs-plugin)

## Gluu OAuth 2.0 client credential authentication

This plugin enables the use of an external OpenID Provider for OAuth2 client registration and authentication. It needs to connect via `https` to Gluu's `oxd-https-extension` service, which is an OAuth2 client middleware service. It provides OAuth 2.0 client credential authentication with three different modes.

### Add API

The first step is to add your API in the kong. Use [API Section](../admin-gui.md#apis) to add API in kong.

### Enable gluu-oauth2-client-auth protection

Use [Manage APIs](../admin-gui.md#manage-apis) section to enable `gluu-oauth2-client-auth` plugin. In the `custom` section, there is `Gluu OAuth2 Client Auth` box. Click on `+` icon to enable plugin.

![add_plugin_api](../img/3_1_add_plugin_api.png)

| **FORM PARAMETER** | **DESCRIPTION** |
|---------------|-----------------|
| oxd id(optional) | Used to introspect the token. By default it fills oxd id from [Config](../configuration.md#admin-gui-portal-konga). You can also enter any other oxd_id. If you leave it as blank then plugin creates new client itself. |
| anonymous(optional) | An optional string (consumer uuid) value to use as an `anonymous` consumer if authentication fails. If empty (default), the request will fail with an authentication failure 4xx. Please note that this value must refer to the Consumer id attribute which is internal to Kong, and not its custom_id. |
| hide credentials(optional) | An optional boolean value telling the plugin to hide the credential to the upstream API server. It will be removed by Kong before proxying the request |

!!! Note
    See all the attribute description in [Admin API](api.md#enable-gluu-oauth2-client-auth-protection) section. 

### Usage

In order to use the plugin, you first need to create a Consumer to associate one or more credentials to. The Consumer represents a developer using the final service/API.

#### Create a Consumer 

You need to associate a credential to an existing Consumer object, that represents a user consuming the API. To create a Consumer you can use [Consumer section](../admin-gui.md#4-consumers).

#### Create OAuth credential

This process registers OpenId client with oxd which help you to get tokens and authenticate the token. Plugin behaves as per selected mode. There are three modes. 

| Mode | DESCRIPTION |
|----------------|-------------|
| oauth_mode | If yes then kong act as OAuth client only. |
| uma_mode | If yes then this indicates your client is a valid UMA client, and obtain and send an RPT as the access token. You must need to configure [gluu-oauth2-rs](https://github.com/GluuFederation/gluu-gateway/tree/master/gluu-oauth2-rs) plugin for uma_mode. |
| mix_mode | If yes then the gluu-oauth2 plugin will try to obtain an UMA RPT token if the RS returns a 401/Unauthorized. You must need to configure [gluu-oauth2-rs](https://github.com/GluuFederation/gluu-gateway/tree/master/gluu-oauth2-rs) plugin for uma_mode. |

Use [Consumer credential configuration](../admin-gui.md#consumer-credential-configuration) section to create OAuth credential. In the `Credential` section, there is `OAuth2` section. Click on `+ Create credential` button.

![consumer_credential_list](../img/4_1_consumer_credential_list.png)

You can see below add OAuth credential form

![consumer_credential_add](../img/4_2_consumer_credential_add.png)

| FORM PARAMETER | DESCRIPTION |
|----------------|-------------|
| name | The name to associate to the credential. In OAuth 2.0 this would be the application name. |
| OAuth mode(semi-optional) | If true, kong act as OAuth client only. |
| UMA mode(semi-optional) | This indicates your client is a valid UMA client, and obtain and send an RPT as the access token. |
| Mix mode(semi-optional) | If Yes, then the gluu-oauth2 plugin will try to obtain an UMA RPT token if the RS returns a 401/Unauthorized. |
| Allow unprotected path(false) | It is used to allow or deny unprotected path by UMA-RS. |
| oxd id(optional) | If you have existing oxd entry then enter oxd_id(also client id, client secret and client id of oxd id). If you have client created from OP server then skip it and enter only client_id and client_secret. |
| client name(optional) | An optional string value for client name. |
| client id(optional) | You can use existing client id. |
| client secret(optional) | You can use existing client secret. |
| client id of oxd id(optional) | You can use existing client id of oxd id. |
| client jwks uri(optional) | An optional string value for client jwks uri. |
| client token endpoint auth method(optional) | An optional string value for client token endpoint auth method. |
| client token endpoint auth signing_alg(optional) | An optional string value for client token endpoint auth signing alg. |

#### Verify that your API is protected by gluu-oauth2-client-auth

You need to pass token as per your authentication mode(oauth_mode, uma_mode, and mix_mode). In oauth_mode and mix_mode, you need to pass oauth2 access token and in uma_mode, you need to RPT token.

```
$ curl -X GET \
    http://localhost:8000/your_api_endpoint \
    -H 'authorization: Bearer 481aa800-5282-4d6c-8001-7dcdf37031eb' \
    -H 'host: your.api.server.com'
```

If your toke is not valid or time expired then you will get failed message.

```
HTTP/1.1 401 Unauthorized
{
    "message": "Unauthorized"
}
```

#### Verify that your API can be accessed with valid token
(This sample assumes that below bearer token is valid and grant by OP server).

```
$ curl -X GET \
    http://localhost:8000/your_api_endpoint \
    -H 'authorization: Bearer 7475ebc5-9b92-4031-b849-c70a0e3024f9' \
    -H 'host: your_api_server'
```

You can use this information on your side to implement additional logic. You can use the X-Consumer-ID value to query the Kong Admin API and retrieve more information about the Consumer.

## Gluu OAuth 2.0 UMA RS Plugin

User-Managed Access Resource Server plugin.

It allows to protect your API (which is proxied by Kong) with [UMA](https://docs.kantarainitiative.org/uma/rec-uma-core.html)

!!! Note
    You must need to configure first **gluu-oauth2-client-auth** plugin.

### Add API

The first step is to add your API in the kong. Use [API Section](../admin-gui.md#apis) to add API in kong.

### Enable gluu-oauth2-rs protection

Use `SECURITY` link in [API](../admin-gui.md#apis) section.

![add_uma_rs](../img/3_add_uma_rs.png)

!!! Note
    See all the attribute description in [Admin API](api.md#gluu-oauth-20-uma-rs-plugin) section. 

You can register your resources using this section. It generate the `scope_expression` json. you can view and confirm your [protection document](#protection-document) by clicking `VIEW RESOURCES JSON` button. 

#### Protection document   

Protection document - json document which describes UMA protection in declarative way and is based on [uma-rs](https://github.com/GluuFederation/uma-rs) project.

 - path - relative path to protect (exact match)
 - httpMethods - GET, HEAD, POST, PUT, DELETE
 - scope - scope required to access given path
 - ticketScopes - optional parameter which may be used to keep ticket scope as narrow as possible. If not specified plugin will register ticket with scopes specified by "scope" which often may be unwanted. (For example scope may have "http://photoz.example.com/dev/actions/all" and the authorized ticket may grant access also to other resources).
    
Lets say we have APIs which we would like to protect:

 - GET https://your.api.server.com/photo  (UMA scope: http://photoz.example.com/dev/actions/view)
 - PUT https://your.api.server.com/photo  (UMA scope: http://photoz.example.com/dev/actions/all or http://photoz.example.com/dev/actions/add)
 - POST https://your.api.server.com/photo  (UMA scope: http://photoz.example.com/dev/actions/all or http://photoz.example.com/dev/actions/add)
 - GET https://your.api.server.com/document  (UMA scope: http://photoz.example.com/dev/actions/view)

Protection document for this sample:

![add_resources](../img/3_3_add_resource.png)

You can see the below json by clicking on `VIEW RESOURCES JSON` button.

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

#### Verify that your API is protected by gluu-oauth2-rs

```
$ curl -i -X GET \
  --url http://localhost:8000/your/api \
  --header 'Host: your.api.server.com'
```

Since you did not specify the required authorized RPT in "Authorization" header (e.g. "Authorization: Bearer vF9dft4qmT"), the response should be 403 Forbidden:

```
HTTP/1.1 403 Forbidden
WWW-Authenticate: UMA realm="rs",
  as_uri="https://uma.server.com",
  error="insufficient_scope",
  ticket="016f84e8-f9b9-11e0-bd6f-0021cc6004de"

{"message":"Unauthorized"}
```

```
HTTP/1.1 403 Forbidden
Warning: 199 - "UMA Authorization Server Unreachable"
```

#### Verify that your API can be accessed with valid RPT
 
(This sample assumes that "481aa800-5282-4d6c-8001-7dcdf37031eb" is valid and authorized by UMA Authorization Server RPT).

```
$ curl -i -X GET \
  --url http://localhost:8000/your/api \
  --header 'Host: your.api.server.com'
  --header 'Authorization: Bearer 481aa800-5282-4d6c-8001-7dcdf37031eb'
```

## Upstream Headers

When a client has been authenticated, the plugin will append some headers to the request before proxying it to the upstream service, so that you can identify the consumer and the end-user in your code:

1. **X-Consumer-ID**, the ID of the Consumer on Kong
2. **X-Consumer-Custom-ID**, the custom_id of the Consumer (if set)
3. **X-Consumer-Username**, the username of the Consumer (if set)
4. **X-Authenticated-Scope**, the comma-separated list of scopes that the end user has authenticated, if available (only if the consumer is not the 'anonymous' consumer)
5. **X-OAuth-Client-ID**, the authenticated client id, if oauth_mode is enabled(only if the consumer is not the 'anonymous' consumer)
6. **X-OAuth-Expiration**, the token expiration time, Integer timestamp, measured in the number of seconds since January 1 1970 UTC, indicating when this token will expire, as defined in JWT RFC7519. It only returns in oauth_mode(only if the consumer is not the 'anonymous' consumer)
7. **X-Anonymous-Consumer**, will be set to true when authentication failed, and the 'anonymous' consumer was set instead.

You can use this information on your side to implement additional logic. You can use the X-Consumer-ID value to query the Kong Admin API and retrieve more information about the Consumer.

# Admin GUI

You can configure plugins using Admin GUI - Konga. There are two plugins. 

!!! Note
    Configure the plugins using [Admin API](./api.md)
    
1. [Gluu OAuth 2.0 client credential authentication](#gluu-oauth-20-client-credential-authentication)
2. [Gluu OAuth 2.0 UMA RS plugin](#gluu-oauth-20-uma-rs-plugin)

## Gluu OAuth 2.0 client credential authentication

This plugin enables the use of an external OpenId Provider for OAuth2 client registration and authentication. It needs to connect via `https` to Gluu's `oxd-https-extension` service, which is an OAuth2 client middleware service. It provides OAuth 2.0 client credential authentication with three different modes.

### Add an API

The first step is to add your API in Kong. Use the [API Section](../admin-gui.md#apis) to add API in Kong.

### Enable gluu-oauth2-client-auth protection

Use the [Manage APIs](../admin-gui.md#manage-apis) section to enable the `gluu-oauth2-client-auth` plugin. In the `custom` section, there is a `Gluu OAuth2 Client Auth` box. Click on the `+` icon to enable the plugin.

![add_plugin_api](../img/3_1_add_plugin_api.png)

| **FORM PARAMETER** | **DESCRIPTION** |
|---------------|-----------------|
| oxd id(optional) | Used to introspect the token. By default it fills oxd_id from [Config](../configuration.md#admin-gui-portal-konga). You can also enter any other oxd_id. If you leave it blank, the plugin creates a new client itself. |
| anonymous(optional) | An optional string (consumer uuid) value to use as an `anonymous` consumer if authentication fails. If empty (default), the request will fail with an authentication failure 4xx. Please note that this value must refer to the Consumer id attribute which is internal to Kong, and not its custom_id. |
| hide credentials(optional) | An optional boolean value telling the plugin to hide the credential to the upstream API server. It will be removed by Kong before proxying the request |

!!! Note
    See all the attribute descriptions in the [Admin API](api.md#enable-gluu-oauth2-client-auth-protection) section. 

In order to use the plugin, you first need to create a Consumer to associate one or more credentials with. The Consumer represents a developer using the final service/API.

### Create a Consumer 

You need to associate a credential with an existing Consumer object which represents a user consuming the API. To create a Consumer, you can use the [Consumer section](../admin-gui.md#consumers).

### Create an OAuth credential

This process registers an OpenId client with oxd which helps you get tokens and authenticate the token. The Plugin behaves as per selected mode. There are three modes. 

| Mode | DESCRIPTION |
|----------------|-------------|
| oauth_mode | If yes, then Kong acts as an OAuth client only. |
| uma_mode | If Yes, then this indicates your client is a valid UMA client, and obtains and sends an RPT as the access token. You need to configure the [gluu-oauth2-rs](https://github.com/GluuFederation/gluu-gateway/tree/master/gluu-oauth2-rs) plugin for uma_mode. |
| mix_mode | If Yes, then the gluu-oauth2 plugin will try to obtain an UMA RPT token if the RS returns 401/Unauthorized. You need to configure the [gluu-oauth2-rs](https://github.com/GluuFederation/gluu-gateway/tree/master/gluu-oauth2-rs) plugin for uma_mode. |

Use the [Consumer credential configuration](../admin-gui.md#consumer-credential-configuration) section to create an OAuth credential. In the `Credential` section, there is an `OAuth2` section. Click on the `+ Create credential` button.

![consumer_credential_list](../img/4_1_consumer_credential_list.png)

Below you can see the add OAuth credential form

![consumer_credential_add](../img/4_2_consumer_credential_add.png)

| FORM PARAMETER | DESCRIPTION |
|----------------|-------------|
| name | The name to associate with the credential. In OAuth 2.0, this would be the application name. |
| OAuth mode(semi-optional) | If True, Kong acts as an OAuth client only. |
| UMA mode(semi-optional) | This indicates your client is a valid UMA client, and obtains and sends an RPT as the access token. |
| Mix mode(semi-optional) | If Yes, then the gluu-oauth2 plugin will try to obtain an UMA RPT token if the RS returns 401/Unauthorized. |
| Allow unprotected path(false) | It is used to allow or deny an unprotected path by UMA-RS. |
| oxd id(optional) | If you have an existing oxd entry, then enter the oxd_id (also client id, client secret and client id of oxd id). If you have a client created from the OP server, skip it and enter only the client_id and client_secret. |
| client name(optional) | An optional string value for the client name. |
| client id(optional) | You can use an existing client id. |
| client secret(optional) | You can use an existing client secret. |
| client id of oxd id(optional) | You can use an existing client id of oxd id. |
| client jwks uri(optional) | An optional string value for a client jwks uri. |
| client token endpoint auth method(optional) | An optional string value for the client token endpoint auth method. |
| client token endpoint auth signing_alg(optional) | An optional string value for the client token endpoint auth signing alg. |

The next step is to configure [Gluu OAuth 2.0 UMA RS plugin](#gluu-oauth-20-uma-rs-plugin).

## Gluu OAuth 2.0 UMA RS Plugin

User-Managed Access Resource Server plugin.

It allows you to protect your API (which is proxied by Kong) with [UMA](https://docs.kantarainitiative.org/uma/rec-uma-core.html)

!!! Note
    You need to configure the **gluu-oauth2-client-auth** plugin first.


### Add an API

The first step is to add your API in Kong. Use the [API Section](../admin-gui.md#apis) to add it in Kong.

### Enable gluu-oauth2-rs protection

Use the `SECURITY` link in the [API](../admin-gui.md#apis) section.

![add_uma_rs](../img/3_add_uma_rs.png)

!!! Note
    See all the attribute descriptions in the [Admin API](api.md#gluu-oauth-20-uma-rs-plugin) section. 

You can register your resources using this section. It generates the `scope_expression` JSON. you can view and confirm your [protection document](#protection-document) by clicking the `VIEW RESOURCES JSON` button. 

#### Protection document   

Protection document - a JSON document which describes UMA protection in a declarative way and is based on the [uma-rs](https://github.com/GluuFederation/uma-rs) project.

 - path - a relative path to protect (exact match)
 - httpMethods - GET, HEAD, POST, PUT, DELETE
 - scope - the scope required to access the given path
 - ticketScopes - an optional parameter which may be used to keep the ticket scope as narrow as possible. If not specified, the plugin will register the ticket with its scopes specified by "scope," which may often be unwanted. (For example, the scope may have "http://photoz.example.com/dev/actions/all" and the authorized ticket may grant access also to other resources).
    
Let's say we have APIs which we would like to protect:

 - GET https://your.api.server.com/photo  (UMA scope: http://photoz.example.com/dev/actions/view)
 - PUT https://your.api.server.com/photo  (UMA scope: http://photoz.example.com/dev/actions/all or http://photoz.example.com/dev/actions/add)
 - POST https://your.api.server.com/photo  (UMA scope: http://photoz.example.com/dev/actions/all or http://photoz.example.com/dev/actions/add)
 - GET https://your.api.server.com/document  (UMA scope: http://photoz.example.com/dev/actions/view)

A protection document for this sample:

![add_resources](../img/3_3_add_resource.png)

You can see the below JSON by clicking on the `VIEW RESOURCES JSON` button.

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

The next step is to access and verify your API using the Kong proxy endpoint.

## Verify you API

After the configuration, you are ready to verify whether your API is protected by plugins or not. You need to pass the token as per configured [authentication mode](#create-an-oauth-credential).

A sample request to the proxy endpoint. You can configure the port for the proxy endpoint using [kong config](../configuration.md#kong).

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

Return 401/Unauthorized when your token is not valid or time expired.

```
HTTP/1.1 401 Unauthorized
{
    "message": "Unauthorized"
}
```

### 403/Forbidden

Return 403/Forbidden when you did not specify the required authorized RPT in the "Authorization" header. Also, you will get the `WWW-Authenticate` header with `ticket`.

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

### 200 Success

If your token is valid, you will get a success response from your upstream API.

## Upstream Headers

When a client has been authenticated, the plugin will append some headers to the request before proxying it to the upstream service, so that you can identify the consumer and the end-user in your code:

1. **X-Consumer-ID**, the ID of the Consumer on Kong
2. **X-Consumer-Custom-ID**, the custom_id of the Consumer (if set)
3. **X-Consumer-Username**, the username of the Consumer (if set)
4. **X-Authenticated-Scope**, the comma-separated list of scopes that the end user has authenticated, if available (only if the consumer is not an 'anonymous' consumer)
5. **X-OAuth-Client-ID**, the authenticated client id, if oauth_mode is enabled (only if the consumer is not an 'anonymous' consumer)
6. **X-OAuth-Expiration**, the token expiration time, Integer timestamp, measured in the number of seconds since January 1, 1970 UTC, indicating when this token will expire, as defined in JWT RFC7519. It only returns in oauth_mode (only if the consumer is not an 'anonymous' consumer)
7. **X-Anonymous-Consumer**, will be set to true when authentication fails, and the 'anonymous' consumer is set instead.

You can use this information on your side to implement additional logic. You can use the X-Consumer-ID value to query the Kong Admin API and retrieve more information about the Consumer.

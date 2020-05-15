# Gluu OpenID Connect and UMA
## Overview
The `gluu-openid-connect` plugin allows you to integrate with Gluu CE Identity Provider(IdP). This plugin working as a proxy OAuth 2.0 resource server(RS) and/or as an OpenID Connect relying party(RP) between the client and the upstream service. 

Plugin providing you the `OpenID Connect Authorization code flow`. You just need to add your upstream service(your website) and `gluu-openid-connect` Plugin, after this when user request to the kong proxy, plugin will redirect user to IdP for authentication, IdP validate the user, redirect back to kong proxy, plugin validate the `id_token` and allows to access to the resources. 

We added a very powerful feature in `gluu-uma-pep` plugin which provides `UMA Claim gathering` authorization. which is easily integrate with `gluu-openid-connect` plugin. After successful user authentication, `gluu-openid-connect` communicate with `gluu-uma-pep` plugin by passing a valid `id_token`. The `gluu-uma-pep` plugin asks the authenticated user to provide some more extra information(claims) to authorize the user. This is used to authorize the user.

Below are the following features available in `gluu-openid-connect` plugin:

- User authentication using **OpenID Connect Authorization code flow** and `ID Token` verification.
- Configurable `ID Token` based authentication user session. This setting used to configure user login session time.
- Stepped-up authentication which is URL Based `acr_values` resource registration and configuration which forces a user to go through one more step to access the resources. For more details check [Gluu CE Docs](https://gluu.org/docs/ce/admin-guide/openid-connect/#authentication) for all available acr technique available in Gluu CE.
- `required_acrs_expression` feature where you have more control to authenticate the resources. You can easily add or remove the authentication on the register resources(URL Path).
- Easy integration and communication with `gluu-uma-pep` plugin for user authorization i.e. `UMA Claim gathering`. You just need to add the `gluu-uma-pep`. 
- Easy integration and communication with `gluu-opa-pep` plugin for user authorization. Check [`gluu-opa-pep`](../plugin/gluu-opa-pep.md) docs for more details.

There are two plugins for OAuth security.

   1. **gluu-openid-connect**: Authenticate user using OpenID Connect authorization code flow. The plugin priority is `997`.
   1. **gluu-uma-pep**: Authorization by UMA scopes. The plugin priority is `995`. `obtain_rpt` and `redirect_claim_gathering_url`, you need to set these two properties for integrating UMA-PEP plugin with OpenID Connect plugin

## Configuration

We recommend enabling the plugin on Route Object because plugin needs correct `redirect_uri`, `post_logout_uri` and `claim_gathering_uri` for authorization code flow.

!!! Important
    konga.log also shows the curl commands for all API requests to Kong and oxd made by the Konga GUI. This curl command can be used to automate configuration instead of using the web interface.

### Add Service

Follow these step to add Service using GG UI
 
- Click [`SERVICES`](../../admin-gui.md/#services) on the left panel
- Click on [`+ ADD NEW SERVICE`](../../admin-guide/services-routes-config/) button
- Fill the form by your upstream service details

### Add Route

Follow these steps to add a route:

- Click on `service name` or `edit` button of above added service
- Click [`ROUTES`](../../admin-guide/services-routes-config/#create-a-new-route-attached-to-the-service)
- Click the [`+ ADD ROUTE`](../../admin-guide/services-routes-config/#create-a-new-route-attached-to-the-service) button
- Fill the form by routing details. Check kong docs for more routing capabilities [here](https://docs.konghq.com/0.14.x/proxy/#routes-and-matching-capabilities).


### Add Plugin

Follow these steps to add `gluu-openid-connect` plugin:

- Click [`ROUTES`](../../admin-guide/services-routes-config/#create-a-new-route-attached-to-the-service) on the left panel
- Click on `route id/name` or `edit` button
- Click on [`Plugins`](../../admin-guide/services-routes-config/#create-a-new-route-attached-to-the-service)
- Click on `+ ADD PLUGIN` button
- You will see `Gluu OIDC & UMA PEP` title and `+` icon in pop-up.

![12_oidc-plugin-add](../img/12_oidc-plugin-add.png)

Clicking on the `+` icon will bring up the below form.

![oidc1](../img/oidc1.png)
![oidc2](../img/oidc2.png)
![oidc3](../img/oidc3.png)

Use this section to setup the headers, which will sent to your upstream service after authnetication.

![oidc3-1](../img/oidc3-1.png)

This section is used to add the `URL Based ACR feature`. Check [here](#dynamic-url-base-acrs-stepped-up-authentication) for more description to configure acr expression. If you do not want to configure the `ACR expression` then just disable using the button which is at the top beside the title. In `no acr expression` case, authentication flow executes with any acr, you may need to set acr at your OP Server side.
![oidc4](../img/oidc4.png)
![oidc5](../img/oidc5.png)
![oidc52](../img/oidc5-2.png)

This section is used to add the `gluu-uma-pep` plugin. Check [here](#uma-scope-expression) for more description to configure uma expression. If you do not want to add `gluu-uma-pep` plugin, you just need to disable it using the button which is just beside of heading "UMA PEP Security Expression". You can see in below screenshot.
![oidc6](../img/oidc6.png)
![oidc7](../img/oidc7.png)
![oidc8](../img/oidc8.png)

#### Configure Plugin using Kong Admin API

!!! Note
    Use [OXD API](https://gluu.org/docs/oxd/) for [client registration](https://gluu.org/docs/oxd/api/#register-site) and [UMA resource registration](https://gluu.org/docs/oxd/api/#uma-rs-protect-resources).

Configuration for `gluu-openid-connect` plugin. See [here](#gluu-openid-connect) all available parameters for plugin.

```
$ curl -X POST \
  http://<kong_hostname>:8001/plugins \
  -H 'Content-Type: application/json' \
  -d '{
  "name": "gluu-openid-connect",
  "config": { <parameters> },
  "route": { "id": "<kong_route_object_id>" }
}'
```

Configuration for `gluu-uma-pep` plugin. See [here](#gluu-uma-pep) all available parameters for plugin.

```
$ curl -X POST \
  http://<kong_hostname>:8001/plugins \
  -H 'Content-Type: application/json' \
  -d '{
  "name": "gluu-uma-pep",
  "config": { <parameters> },
  "route": { "id": "<kong_route_object_id>" }
}'
```

### Parameters

Here is a list of all the parameters which can be used in this plugin's configuration.

#### Gluu-OpenID-Connect

| field | Default | Description |
|-------|---------|-------------|
|**op_url**||The URL of your OP server. Example: https://op.server.com|
|**oxd_url**||The URL of your oxd server. Example: https://oxd.server.com|
|**oxd_id**|| The ID for an existing client, used to introspect the token. If left blank, a new client will be registered dynamically |
|**client_id**|| An existing client ID, used to get a protection access token to access the introspection API. Required if an existing oxd ID is provided.|
|**client_secret**|| An existing client secret, used to get protection access token to access the introspection API. Required if an existing oxd ID is provided.|
|**authorization_redirect_path**|| Redirect URL for your OP Client. You just need to set path here like `/callback` but you need to register OP Client with full URL like `https://kong.proxy.com/callback`. GG UI creates OP client for you and also register the gluu-openid-connect plugin.|
|**logout_path**|| Use this endpoint to request logout. Example: `/logout`. When you request this endpoint, plugin will first clear the current session and then redirect to OP for logout.|
|**post_logout_redirect_path_or_url**||Post logout redirect URL for your OP Client. You can set here internal kong proxy path(example: `/post_logout`) or you can set any external url also|
|**requested_scopes**||Scopes: ['email', 'openid', 'profile']|
|**required_acrs_expression**(optional)||Check [here](#dynamic-url-base-acrs-stepped-up-authentication) for details|
|**max_id_token_age**||Maximum age of `id token` in seconds |
|**max_id_token_auth_age**||Maximum authentication age of `id_token` in seconds |
|**custom_headers**||Used to set the custom headers, which is passed to upstream service by kong after authentication. Check [here for more details](../common-features/#custom-headers)|
|**restore_original_auth_params**|false|It use to restore original request's method, content-related headers and body after successful redirection from OP. It is useful if original request has methods like `POST`, `PUT`, `PATCH`, `DELETE`.|

#### Dynamic URL Base ACRs stepped up authentication

It is stringify json, Used to configure URL Based ACRs Configuration - Stepped Up Authentication. If you do not configure ACR expression, authentication flow will execute with any acr, you may need to set acr at your IdP side. Below is the structure of the `required_acrs_expression`.

- `path`: it is your url which you want to protect. There is regular expression facility for path configuration. Check [here](../common-features/#dynamic-resource-protection) for more dynamic path registration details.
    - `condition`: it is the array of conditions for the path where you can define acr values to the path. You can add multiple conditions with different Http Method.
        - `httpMethods`: it is HTTP Method. During authentication, the plugin uses it as a filter for the request. **`?`** in the HTTP method allow all the http methods. It should be in a capital case. e.g. GET, POST, PUT.
        - `no_auth`: If it is true then plugin doesn't perform any authentication and just allow the requested resources. If it is false that means you want to add authentication and for that, you need to configure `required_acrs`.
        - `required_acrs`: It is used to specify the `acr` values which you wanted to apply on a path.

Example of JSON expression
```
[
  {
    "path": "/users/??",
    "conditions": [
      {
        "httpMethods": [
          "?"
        ],
        "no_auth": false,
        "required_acrs": [
          "otp"
        ]
      }
    ]
  },
  {
    "path": "/??",
    "conditions": [
      {
        "httpMethods": [
          "?"
        ],
        "no_auth": false,
        "required_acrs": [
          "auth_ldap_server"
        ]
      }
    ]
  },
  {
    "path": "/home/??",
    "conditions": [
      {
        "httpMethods": [
          "?"
        ],
        "no_auth": true
      }
    ]
  }
]
```

JSON expression in string format(stringify json)
```
"[{\"path\":\"/users/??\",\"conditions\":[{\"httpMethods\":[\"?\"],\"no_auth\":false,\"required_acrs\":[\"otp\"]}]},{\"path\":\"/??\",\"conditions\":[{\"httpMethods\":[\"?\"],\"no_auth\":false,\"required_acrs\":[\"auth_ldap_server\"]}]},{\"path\":\"/home/??\",\"conditions\":[{\"httpMethods\":[\"?\"],\"no_auth\":true}]}]"
```

!!! Info
    `?` in the HTTP method means to allow all the http methods.

![oidc4](../img/oidc4.png)
![oidc5](../img/oidc5.png)
![oidc52](../img/oidc5-2.png)

As per the above example, 

- For `/users/??` path, GG will initiate the `OTP` stepped up authentication 
- For other paths `/??`, GG will perform the `auth_ldap_server` authentication
- For `/home` there is `no_auth = true` set, which means the plugin will not perform any authentication. You just need to set `no_auth = true` for a path where you do not want to perform any authentication and just serve the request resources. 

#### Gluu-UMA-PEP 

| field | Default | Description |
|-------|---------|-------------|
|**op_url**||The URL of your OP server. Example: https://op.server.com|
|**oxd_url**||The URL of your oxd server. Example: https://oxd.server.com|
|**oxd_id**|| The ID for an existing client used to introspect the token. If left blank, a new client will be registered dynamically |
|**client_id**|| An existing client ID used to get a protection access token to access the introspection API. Required if an existing oxd ID is provided.|
|**client_secret**|| An existing client secret, used to get a protection access token to access the introspection API. Required if an existing oxd ID is provided.|
|**uma_scope_expression**|| Used to add scope security on an UMA scope token. The UMA Scope Expression is a JSON expression, used to register the resources in a resource server. See more details in the [Gluu Server docs](https://gluu.org/docs/ce/admin-guide/uma/#scopes-expressions). You can register a more dynamic path, there are 3 elements to make more dynamic path registration and protection. Check [here](../../plugin/gluu-uma-auth-pep/#uma-scope-expression) for more details.|
|**deny_by_default**| true | For paths not protected by UMA scope expressions. If true, denies unprotected paths.|
|**require_id_token**|false| This is for Push Claim token. if it is true then it will use id_token for push claim token for getting RPT|
|**obtain_rpt**|false|It is used to get RPT when you configure `gluu-openid-connect` plugin with `gluu-uma-pep`|
|**claims_redirect_path**||Claims redirect URL in claim gathering flow for your OP Client. You just need to set path here like `/claim-callback` but you need to register OP Client with full URL like `https://kong.proxy.com/claim-callback`. GG UI creates OP client for you and also configure the `gluu-openid-connect` and `gluu-uma-pep` plugin.|
|**redirect_claim_gathering_url**|false|It used to tell the plugin that if `need_info` response comes in claim gathering situation then redirect it for claim gathering.|
|**method_path_tree**||It is for plugin internal use. We use it for tree level matching for dynamic paths which registered in `uma_scope_expression`| 
|**pushed_claims_lua_exp**||Used to make the Push claim token. Check [here](#configure-pushed-claims-lua-expression) for more details.|

!!! Note
    GG UI can create a dynamic client. However, if the Kong Admin API is used for plugin configuration, it requires an existing client using the oxd API, then passing the client's credentials to the Gluu-OpenID-Connect and Gluu-UMA-PEP plugin.

#### Configure Pushed Claims Lua Expression

You can pass some more extra information to [UMA Policy](https://www.gluu.org/docs/ce/admin-guide/uma/#uma-rpt-authorization-policies) so that you can make more configurable authorization policy using UMA and UMA Policies. After OpenID Connect authentication, you can pass userinfo to UMA Policy for authorization. So `gluu-uma-pep` has facility to build a custom Push Claim token. It provides 3 environments

|Environments|Description| 
|------------|-----------|
|`id_token`|This environment has the decoded ID Token|
|`userinfo`|This environment has the user info which is the response of the `/useinfo` endpoint|
|`request`|This environment has the all the value of HTTP Request object. check [here](https://docs.konghq.com/2.0.x/pdk/kong.request/) for values of request object. For example: If you want to get http request method then set value as `{method = request.get_method()}`|

For Example: If you want to pass the userinfo to UMA policy then you need to configure `pushed_claims_lua_exp = userinfo`. This is Lua expression so plugin will pass this as it is. After this you can easily get this information in your `UMA RPT Policy` For Example: `context.getClaim("name")`, `context.getClaim("email")`. 

More Examples:

|Configuration|Use in Policy Script|
|-------------|---|
|`id_token`|<ul><li>You will have all the fields of decoded id_token.</li><li>`context.getClaim("exp")`</li><li>`context.getClaim("aud")`</li><li>`context.getClaim("auth_time")`</li></ul>|
|`userinfo`|<ul><li>You will have all the fields of userinfo response.</li><li>`context.getClaim("name")`</li><li>`context.getClaim("email")`</li><li>`context.getClaim("sub")`</li></ul>|
|`{id_token=id_token,userinfo=userinfo}`|<ul><li>Here you are passing both values as a single object. So you will have values of both `userinfo` and `id_token`. Here we are using `{}` to make a object.</li><li>`context.getClaim("id_token").optString("aud")`</li><li>`context.getClaim("userinfo").getString("name")`</li></ul>|
|`{name=userinfo.name}`|<ul><li>You have here only one field i.e. `name`</li><li>`context.getClaim("name")`</li></ul>|
|`{path=request.get_path(), method=request.get_method(), headers=request.get_headers(), specific_header_host=request.get_header('Host')}`|<ul><li>`context.getClaim("path")`</li><li>`context.getClaim("method")`</li><li>`context.getClaim("headers").getString("host")` and etc in headers</li><li>`context.getClaim("specific_header_host")`</li></ul>|

### Session Configuration

Pluging uses [lua-resty-session](https://github.com/bungle/lua-resty-session) for user session management. It provides several pluggable storage adapters to store session.

| Adapter | Description | Configuration Command |
|---------|-------------|---------------|
|`cookie`|Client Side Cookie (this is the default adapter)|`set $session_storage cookie;`|
|`shm`|Lua Shared Dictionary|`set $session_storage shm;`|
|`memcache`|Memcached Storage Backend|`set $session_storage memcache;`|
|`redis`|Redis Backend|`set $session_storage redis;`|
|`dshm`|DSHM Storage Adapter|`set $session_storage dshm;`|

Some important point:

* With default cookie storage Cookie header starts from 4k size and it may be a problem for some web servers.

* Obviously Lua Shared Dictionary doesn't work for a cluster.

* Memcached/Redis/DSHM work well in distributed environment, but require some additional efforts.

* An admin has a choice to select and configure any storage. For detailed description check [here](https://github.com/bungle/lua-resty-session#pluggable-storage-adapters).

* To set configuration, we recommend make one file with any name for example: `/etc/kong/injected_http.conf` and save the configuration command in this config file. Include this file in `/etc/kong/kong.conf` using environment Kong variable `KONG_NGINX_PROXY_INCLUDE=/etc/kong/injected_http.conf` or you can add `nginx_proxy_include=/etc/kong/injected_http.conf` in kong.conf. Keep in mind that this config will be shared between all gluu-openid-connect plugins.

#### Session secret in cluster

If you are in cluster, you need to set same session secret for all nodes. Include the same `injected_http.conf` file on every node:
```
set $session_secret <your_secret>;
set $session_storage <your storage>;
<storage configuration parameters>
```
## DB-Less Configuration

For DB-Less configuration you need `1 OP client`. You need to use OXD APIs to create OP Client.

### Plugin Client

This client will be used to configure the plugin.

Below is the curl to create client:

```bash
curl -k -X POST https://<oxd_host>:8443/register-site \
-d '{
  "op_host":"<your_op_server_url>",
  "oxd_url":"<your_oxd_url>",
  "redirect_uris":["https://<gg_host>/callback"],
  "client_name":"oidc-plugin-client",
  "post_logout_redirect_uris":["https://<gg_host>/logout_redirect_uri"],
  "scope":["openid","oxd","email","profile","uma_protection"],
  "grant_types":["client_credentials","authorization_code","refresh_token"],
  "claims_redirect_uri":["https://<gg_host>/claims_callback"]
  }'
```

The `redirect_uris`, `post_logout_redirect_uris` and `claims_redirect_uri` is depende on your route configuration. As per below route configured there is only the host in route so in above `curl` request there is only `<gg_host>`. 

If you have host and path in route e.g. `/auth` then you should have to add `<gg_host>/auth` in URIs, your configuration should be look like:

```
"redirect_uris":["https://<gg_host>/auth/callback"]
"post_logout_redirect_uris":["https://<gg_host>/auth/logout_redirect_uri"]
"claims_redirect_uri":["https://<gg_host>/auth/claims_callback"]
```

### Register the UMA resource

If you want to configure the `gluu-uma-pep` plugin with `gluu-openid-connect` plugin then you also need to register UMA resources with Plugin OP Client.

You need to first get AT using `/get-client-token` endpoint.

```bash
curl -k -X POST https://<your_oxd_host>:8443/get-client-token \
-d '{
  "op_host":"<your_op_server_url>",
  "client_id":"<plugin_client_id>",
  "client_secret":"<plugin_client_secret>",
  "scope":["openid","oxd"]
  }'
```

It will return AT token, you need to use this token in `/uma-rs-protect` request. Below is the `curl` for resource registration. We are registering `scope_expression`. Below is just a **example**. Please check [here](../plugin/gluu-uma-auth-pep.md/#uma-scope-expression) for more information about `scope_expression`.

```bash
curl -k -X POST https://ce-dev6.gluu.org:8443/uma-rs-protect \
-H 'Authorization: Bearer <AT_Token>' \
-d '{
  "oxd_id":"<plugin_client_oxd_id>",
  "resources":[{"path":"/settings/??","conditions":[{"httpMethods":["GET"],"scope_expression":{"rule":{"and":[{"var":0}]},"data":["with-claims"]}}]}]
  }'
```

### Plugin configuration

Below is the example of `kong.yml` with plugin configurations. For more details about plugin parameters chech [here](#parameters).

`uma_scope_expression` is same as `resources` which you registered in `/uma-rs-protect`, it is only in JSON Stringify format.


```json
{
  "_format_version": "1.1",
  "routes": [
    {
      "hosts": [
        "<your_host>"
      ],
      "name": "demo-route",
      "service": "demo-service"
    }
  ],
  "services": [
    {
      "name": "demo-service",
      "url": "<your_upstream_app_url>"
    }
  ],
  "plugins": [
    {
      "name": "gluu-openid-connect",
      "route": "demo-route",
      "config": {
        "oxd_id": "<plugin_clients_oxd_id>",
        "oxd_url": "https://<oxd_host>:8443",
        "client_id": "<plugin_client_id>",
        "client_secret": "<plugin_client_secret>",
        "op_url": "<your_op_server_url>",
        "restore_original_auth_params": false,
        "authorization_redirect_path": "/callback", // as per route configuration
        "logout_path": "/logout", // as per route configuration
        "post_logout_redirect_path_or_url": "/logout_redirect_uri", // as per route configuration
        "requested_scopes": [
          "openid",
          "oxd",
          "email",
          "profile",
          "uma_protection"
        ],
        "required_acrs_expression": "[{\"path\":\"/users/??\",\"conditions\":[{\"httpMethods\":[\"?\"],\"required_acrs\":[\"otp\"],\"no_auth\":false}]},{\"path\":\"/??\",\"conditions\":[{\"httpMethods\":[\"?\"],\"required_acrs\":[\"simple_password_auth\"],\"no_auth\":false}]},{\"path\":\"/home\",\"conditions\":[{\"httpMethods\":[\"GET\"],\"no_auth\":true}]}]", // just example
        "max_id_token_age": 3600,
        "max_id_token_auth_age": 3600,
        "custom_headers": [
          {
            "header_name": "http-kong-userinfo",
            "value_lua_exp": "userinfo",
            "format": "jwt",
            "iterate": false,
            "sep": " "
          },
          {
            "header_name": "http-kong-id-token",
            "value_lua_exp": "id_token",
            "format": "jwt",
            "iterate": false,
            "sep": " "
          }
        ]
      }
    },
    {
      "name": "gluu-uma-pep",
      "route": "demo-route",
      "config": {
        "oxd_id": "<plugin_clients_oxd_id>",
        "oxd_url": "https://<oxd_host>:8443",
        "client_id": "<plugin_client_id>",
        "client_secret": "<plugin_client_secret>",
        "op_url": "<your_op_server_url>",
        "uma_scope_expression": "[{\"path\":\"/settings/??\",\"conditions\":[{\"httpMethods\":[\"GET\"],\"scope_expression\":{\"rule\":{\"and\":[{\"var\":0}]},\"data\":[\"with-claims\"]}}]}]", // just example
        "deny_by_default": false,
        "require_id_token": true,
        "obtain_rpt": true,
        "redirect_claim_gathering_url": true,
        "claims_redirect_path": "/claims_callback", // as per route configuration
        "pushed_claims_lua_exp": "{id_token=id_token,userinfo=userinfo}"
      }
    }
  ]
}
```

## Usage

### Security & Access Proxy

After configuration just hit your proxy endpoint in a browser.

!!! Note
    Kong normally provides the 8443 port for https by default, but during the setup script installation, it is changed to 443.

## Upstream Headers

When a client has been authenticated, the plugin will append headers to the request before proxying it to the upstream service to identify the request and the end-user.

Check [here](../common-features/#custom-headers) to setup custom headers.


## Demos and Tutorials

1. [OpenID Connect Authentication and UMA PEP Authorization](/tutorials/oidc-uma-tutorial/)

1. [OpenID Connect Stepped-up Authentication Tutorial](/tutorials/oidc-steppedup-auth-tutorial/)

1. [OpenID Connect Authentication and OPA PEP Authorization Tutorial](/tutorials/oidc-opa-tutorial/)

1. [Shell script for configuration and quick test](https://github.com/GluuFederation/gluu-gateway-setup/blob/version_4.2.0/scripts/run_tests.sh#L191)

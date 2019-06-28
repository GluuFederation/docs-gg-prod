# Gluu OPA PEP
## Overview

**`gluu-opa-pep`** authorization plugin which interact with the [Open Policy Agent](https://www.openpolicyagent.org/). It is execute after the gluu authentication plugins i.e. `gluu-openid-connect` and `gluu-opa-pep`. After authentication, data will be pass to `gluu-opa-pep` plugin and `gluu-opa-pep` plugin will call the OPA authorization endpoint. You need to configure the endpoint in `gluu-opa-pep` plugin. Plugin priority is `996`.

!!! important
    You can configured only one PEP plugin for authentication plugin. If you configured `gluu-uma-pep` or `gluu-oauth-pep` then GG not allow you to configure `gluu-opa-pep`.

### The data which pass to OPA Policy endpoint after authentication: 

Json Sample
```json
{
  "input": {
    "request_token_data": <object>,
    "userinfo": <object>, -->  only in gluu-openid-connect case
    "path": <Array>,
    "method": <String>,
    "headers": <object>,
    "uri_args": <object>,
    "body": <object> --> request body, configurable using forward_request_body
  }
}
```

**`request_token_data`** data is depend on which authentication plugin you configured.

- `gluu-openid-connect` case : 
  
     Below is the example of data when authentication plugin is `gluu-openid-connect`. You will get `id_token` detail in `request_token_data` property and User info in the `userinfo` property.
   ```json
   {
     "input": {
       "request_token_data": {
         "iat": 1561720857,
         "iss": "https://gluu.local.org",
         "aud": "28275837-c059-4116-b91e-8a11b5b265bb",
         "nonce": "uld7q3v7shmbfg1qsj9uq7gun",
         "oxOpenIDConnectVersion": "openidconnect-1.0",
         "amr": {},
         "acr": "otp u2f",
         "sub": "iaxNrWQcqRGKHPGaIIFA9EFhF6_NiAe5i_slsEbG17U",
         "auth_time": 1561720856,
         "exp": 1561724457,
         "at_hash": "rKgnYSby9lLb95jHl7gWNw"
       },
       "path": [
         "todos"
       ],
       "method": "GET",
       "headers": {
         "host": "gluu.local.org:8443",
         "x-openid-connect-userinfo": "eyJzdWIiOiJpYXhOcldRY3FSR0tIUEdhSUlGQTlFRmhGNl9OaUFlNWlfc2xzRWJHMTdVIn0=",
         "connection": "keep-alive",
         "upgrade-insecure-requests": "1",
         "cache-control": "max-age=0",
         "referer": "https://gluu.local.org/oxauth/auth/otp/otplogin.htm",
         "accept-encoding": "gzip, deflate, br",
         "user-agent": "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/75.0.3770.100 Safari/537.36",
         "cookie": "org.gluu.i18n.Locale=en; session_id=bb60e70e-6c06-4989-b6fa-12e5cb123856; session_state=f9a6003fcebc9a0b7d6527f34c26236820891e1e5d2662bd6d27687005e37afc.4bc1c2ea-973f-4afe-a0c4-f5b2c83aa540; opbs=ea571cc8-68a7-47d2-89aa-e2311623545a; session=_HSgtDYJ0x7c0sj5rJrd2A..|1561724457|ochy-O506ZWLs_ofoHSCcWCpr6tMrysWnvTSfsp7MLUsSAxUYC-OCfdHaqN4weEjDCPjCUFMoWmsBO7OBvKd6stu0umUGUJFeiNNgbkbsNFF9jNq6YvCebgaPE80BrcYfLNCMdDe5vrUycUwBt0lI5WESpuQ6r3YE74EK1Oj0j5HoTh8fr7-5QPkl4cjpTMPzXSpmwLHoeOu8h5fQqJb2SvCKngxwryI-qWB42gpeC8gkxYJl425dr5AtYHwSpcs98mkIuhJXZm0or6GmQcm76z8pfzeZdpqSwR1ymPl98LlxojfEQh2lbsX9a96xwa2ZqwmUnqRYlvx25Taf7Pr045qfyplrLYtx5Mun5UZGUKpJEAbVwymcC4vfvNhayBdWdP5vWvLF-gq1W1OkHf11OEYZOunIIF1mlFrGBovpDFdhecRuLQNCC8OycAgVK0FmulzSVLv1M3GxY7TESttfac7VRmX1wF3gbbFAwcfnMsv39q0u2Wegxr2O6v9m-ewl__1isATQBGlYeP18ODQXdO0AdZPxoV69JrSnlLO0qi6_Yz3DY4zknahF93I99HnDRZTyQRW9_4DvX9HsxK9MFXadfFakX_Cy9zhLrUyvDzVZaVi_sSCXN2nwqejhGPNsgDbDNcxSqebf3g-KEm1VCtS342_5jok2QslEtCap0VDG2kfhYPyMCeVPcJHme4Bj2m2ULtPnSlgjvn9tQyZkwE7PM0t0jCT6pU8hTXgya0j5S9cs-3t8vQ1w_v3cwHAtjI1QeDufoO7wx2fvKslpUslM89V5shQmn5jkONaPScLdcTKrlcJsa2htwJpYdCCrFejQziUJmOUPF60fTb872ggSuoqu3nFhuxrEuzVcO5hGMAh_t5F6wKW7YyGfhjWs7RCnJ9JvqkL2W92jgjBkryHuED74uJQ8q6YHJxZ4e8UVtgTdk70hcNT_Ibf0eM4nabtSphovrx9lbERjSswJxJOvzUQ1aLIMbx9Fx5DTqvMRjxyhB6bBwF2Nun9kmM09NJE6VV4MEa3rVuqey3vDRGW41uhvGoM5LJTln6HSDd_Fky9XrEPuYaQvZtjYYHrH2BDM6Y7XehpvXjUh2uiysz1LjhVPkAN8jasx5tCpYEq5f-egBfYjtL0_ZrE1UI8vB27so0KtHOAv10F4CLWdy2Lq6peNAcBrnk26ZogwVMdBtJmDMREuXS91_2GNN8CpwXfia5DkUMou1JrcbsgRgAytdSdt7p3sKT8ptWrvBvEUhU72i5cN8x2VBff8owOAcr0ZEiOxxQR0h_dsDjQSmCll9ykQzjLfpNiQc0nCy3Sq9IpWSHfTe1lblTJDJQiPDDQxoZ6-FPyndK9KM6sYk0CKWyZpd1HCQpW-RezTBxpcpQrNI7M3wMCJx61-SEvSLTTCYOYOHbbwBALaiVInLlRhuMDm3QfCyMJ7RTqmOrhxrqn6nAQYxzTJZVkBM0rA7_RO_Bdql6O4f2Sw5SuCz3GPJzvdKx8uEoOOyhQXo4uAFK25yGeJRP3St_bKDjwy7qgqmlxU06D04WMp7fNkbGp5Nrh4zpvmwO5iod3EjT3Z3WZ0p70mZiQSUE8ktaHkwHneNC1U1RxyzbjZQMO2qgzH6nIAvqBsX9XGFH2QBnL0VdomWDH3TjQtYwovbYVWjFLLgEPZ4NWEGK46x-UC0etWwsmtu8R78EuyYbgN7hO-l6mmRZFemAX5DI-XEOLwFM8kFM9yH7bULWosQ4UqBM4x-u252zqALVOhPT4rbME6vYWy67phsBD2oj00ZJtKl5NPWWGSZPA2pDkVZuwi5boL4bAd0tDQNucg_3124sufC4rFWhnQ0Mxx2gvaTIaUEYtYk3AbR1W_fFZrSvaZQFCki0Qn7pkmdVORuzop3AeZyAL9s6evJz8Lj7fb9CSHT4e2pcnqVsT6oSD7JQFLw..|YrV4v3SYctgR6YUAiYOLm4e5a6k.",
         "x-openid-connect-idtoken": "eyJpYXQiOjE1NjE3MjA4NTcsImlzcyI6Imh0dHBzOlwvXC9nbHV1LmxvY2FsLm9yZyIsImF1ZCI6IjI4Mjc1ODM3LWMwNTktNDExNi1iOTFlLThhMTFiNWIyNjViYiIsIm5vbmNlIjoidWxkN3EzdjdzaG1iZmcxcXNqOXVxN2d1biIsIm94T3BlbklEQ29ubmVjdFZlcnNpb24iOiJvcGVuaWRjb25uZWN0LTEuMCIsImFtciI6e30sImFjciI6Im90cCB1MmYiLCJzdWIiOiJpYXhOcldRY3FSR0tIUEdhSUlGQTlFRmhGNl9OaUFlNWlfc2xzRWJHMTdVIiwiYXV0aF90aW1lIjoxNTYxNzIwODU2LCJleHAiOjE1NjE3MjQ0NTcsImF0X2hhc2giOiJyS2duWVNieTlsTGI5NWpIbDdnV053In0=",
         "accept-language": "en-US,en;q=0.9",
         "accept": "text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3"
       },
       "uri_args": {
         "id": "1"
       },
       "userinfo": {
         "sub": "iaxNrWQcqRGKHPGaIIFA9EFhF6_NiAe5i_slsEbG17U"
       }
     }
   }
   ```

- `gluu-opa-pep` case: 

     Below is the example of data when authentication plugin is `gluu-opa-pep`. You will get `token introspection` detail in `request_token_data` property.
   ```json
   {
     "input": {
       "request_token_data": {
         "consumer": {
           "custom_id": "9cfe6840-1af1-4718-baf0-c77dfba60ed7",
           "created_at": 1561722475,
           "username": "oauthtest",
           "id": "ad0dc361-6d11-4785-b451-96c04ae514b7"
         },
         "iat": 1561722417,
         "scope": [
           "openid",
           "uma_protection",
           "oxd"
         ],
         "aud": "9cfe6840-1af1-4718-baf0-c77dfba60ed7",
         "token_type": "bearer",
         "exp": 1561722717,
         "client_id": "9cfe6840-1af1-4718-baf0-c77dfba60ed7",
         "iss": "https://gluu.local.org",
         "x5t#S256": "",
         "sub": "UwOkYKzGcIB59t7QqsNH_cqGM1gCN8jDQwOxf0GAnlc"
       },
       "path": [
         "todos"
       ],
       "method": "GET",
       "headers": {
         "host": "test.org",
         "authorization": "Bearer eyJraWQiOiI1YWJlYWY5Yi0zNWFlLTRiZjUtYTBlOC02YzlkYTg3OGZiMjAiLCJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9.eyJhdWQiOiI5Y2ZlNjg0MC0xYWYxLTQ3MTgtYmFmMC1jNzdkZmJhNjBlZDciLCJzdWIiOiJVd09rWUt6R2NJQjU5dDdRcXNOSF9jcUdNMWdDTjhqRFF3T3hmMEdBbmxjIiwieDV0I1MyNTYiOiIiLCJzY29wZSI6WyJvcGVuaWQiLCJ1bWFfcHJvdGVjdGlvbiIsIm94ZCJdLCJpc3MiOiJodHRwczovL2dsdXUubG9jYWwub3JnIiwidG9rZW5fdHlwZSI6ImJlYXJlciIsImV4cCI6MTU2MTcyMjcxNywiaWF0IjoxNTYxNzIyNDE3LCJjbGllbnRfaWQiOiI5Y2ZlNjg0MC0xYWYxLTQ3MTgtYmFmMC1jNzdkZmJhNjBlZDcifQ.aNjj7EChzrGlvJRD28ncANCll0ZxbgqpDdZl7h-8IT-IKTh7wJX1a-7zxfI5mVwdrxydB7ytUUXJLxNk46xZHXULJwarS7Jv1Q2AGjADWh8JhuHiTMjRoO5MgkU_zUXY-QQ9Rxmkl0rCgpvSwbLlIzUJcJOv0CRIlxekUNaMBF1Yic01Nttd0jUZXpiknAuwsKUjmE5CY5AoG2uBBxEF4urMJ0UKQ_WL4U-b9gLOpuUy936dp8dFHy4NN0ouWVDLA6SwZ76HviswAMOAfSeoyuiSSt6i77UGKxbhScH6odjAhavlwdrn6EvyKhQEvWL2i5vOGxF6w3klyDUNKB1Evg",
         "postman-token": "5b45bc63-56fe-4af8-98c8-8f9d9aea19e2",
         "accept": "*/*",
         "x-oauth-client-id": "9cfe6840-1af1-4718-baf0-c77dfba60ed7",
         "x-authenticated-scope": "openid, uma_protection, oxd",
         "connection": "keep-alive",
         "cache-control": "no-cache",
         "cookie": "session=m8khnS2o_kjb8hoh9tFnHQ..|1559560462|yp1dcDTfI0je88qGlxzjbzFxhymgrZv5doiwi1MquNI.|83BB0DbEK5YlkpzaJse2FQIcByo.; org.gluu.i18n.Locale=en",
         "accept-encoding": "gzip, deflate",
         "user-agent": "PostmanRuntime/7.15.0",
         "x-consumer-id": "ad0dc361-6d11-4785-b451-96c04ae514b7",
         "x-consumer-custom-id": "9cfe6840-1af1-4718-baf0-c77dfba60ed7",
         "x-consumer-username": "oauthtest",
         "x-oauth-expiration": "1561722717"
       },
       "uri_args": {}
     }
   }
   ```

## Configuration

Plugins can be configured at the **Service**, **Route** or **Global** level. There are several possibilities for plugin configuration with services and routes. For information on plugin precedence, [read the Kong docs](https://docs.konghq.com/0.14.x/admin-api/#precedence).

### Service Level

#### Add a Service using GG UI

Use the [Service section](../admin-gui/#add-service) of the GG UI doc to add a service using GG UI.

![3_service_list](../img/3_1_service_list.png)

#### Add a Service using Kong Admin API

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

Use the [Manage Service](../admin-gui/#manage-service) section in the GG UI to enable the Gluu OAuth PEP plugin. In the security category, there is a Gluu OAuth PEP box. Click on the **+** icon to enable the plugin.

![11_path_oauth_service](../img/17_opa_service_add.png)

Clicking on the **+** icon will bring up the below form.

!!! important
    If you don't wanna add `gluu-oauth-pep` plugin then disable button which is on the top.

![11_path_add_oauth_service](../img/17_opa_form.png)

#### Configure a Service Plugin using Kong Admin API

Configuration for `gluu-opa-pep`

```
$ curl -X POST \
  http://<kong_hostname>:8001/plugins \
  -H 'Content-Type: application/json' \
  -d '{
  "name": "gluu-opa-pep",
  "config": {
    "opa_url": "<opa_url>",
    "forward_request_body": <false|true>
  },
  "service_id": "<kong_service_object_id>"
}'
```

!!! Note
    Kong does not allow proxying using only a service object--this feature requires a route. At minimum, one service is needed to register an Upstream API and one route is needed for proxying.

### Route Level

#### Add a Route using GG UI

Use the [Manage Service Section](../admin-gui/#service-routes) to add a route using the GG UI.

![3_4_service_route](../img/3_4_service_route.png)

#### Add a Route using Kong Admin API

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

#### Configure a Route Plugin using GG UI

Use the [Manage Route](../admin-gui/#manage-route) section in the GG UI to enable the Gluu OAuth PEP plugin. In the security category, there is a Gluu OAuth PEP box. Click on the **+** icon to enable the plugin.

![12_path_oauth_route](../img/17_opa_route_add.png)

Clicking on the **+** icon will bring up the below form.
![11_path_add_oauth_service](../img/17_opa_form.png)


#### Configure Route Plugin using Kong Admin API

!!! Note
    Use [OXD API](https://gluu.org/docs/oxd/4.0/) for [client registration](https://gluu.org/docs/oxd/4.0/api/#register-site).

Configuration for `gluu-opa-pep`

```
$ curl -X POST \
  http://<kong_hostname>:8001/plugins \
  -H 'Content-Type: application/json' \
  -d '{
  -d '{
  "name": "gluu-opa-pep",
  "config": {
    "opa_url": "<opa_url>",
    "forward_request_body": <false|true>
  },
  "route_id": "<kong_route_object_id>"
}'
```

### Global Plugin

A global plugin will apply to all services and routes.

#### Configure a Global Plugin using GG UI

Use the [Plugin section](../admin-gui/#add-plugin) in the GG UI to enable the Gluu OAuth PEP plugin. In the security category, there is a `Gluu OAuth PEP` box. Click on the **+** icon to enable the plugin.

![5_plugins_add](../img/17_global_add.png)

Clicking on the **+** icon will bring up the below form.
![11_path_add_oauth_service](../img/17_opa_form.png)

#### Configure a Global Plugin using Kong Admin API

!!! Note
    Use [OXD API](https://gluu.org/docs/oxd/4.0/) for [client registration](https://gluu.org/docs/oxd/4.0/api/#register-site).

```
$ curl -X POST \
  http://<kong_hostname>:8001/plugins \
  -H 'Content-Type: application/json' \
  -d '{
  "name": "gluu-opa-pep",
  "config": {
    "opa_url": "<opa_url>",
    "forward_request_body": <false|true>
  },
}'
```

### Parameters

The following parameters can be used in this plugin's configuration.

| field | Default | Description |
|-------|---------|-------------|
|**opa_url**||This is your OPA policy endpoint where authentication data will be pass.|
|**forward_request_body**|false|Forward request body to OPA policy endpoint.|


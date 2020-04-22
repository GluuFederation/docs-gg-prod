# Common Features

Below are common features in the Gluu OAuth and UMA plugins.

## Phantom Token

Some deployments require the use of a bearer token outside of the internal network and a JWT token within it. This phantom token approach is similar to a reverse proxy, adding a layer of insulation between external requests and the internal network.

![phantom_token](../img/phantom_token.png)
 
This feature is available in both the [`gluu-oauth-auth`](../gluu-oauth-auth-pep) and the [`gluu-uma-auth`](../gluu-uma-auth-pep) plugins. To configure phantom token feature, you just need to set `pass_credentials='phantom_token'` in plugin configuration. 

!!! Important
    Set `access_token_as_jwt: false` and `rpt_as_jwt: false` in [client registration](../../admin-guide/consumer-config/), otherwise client by default returns you access token as JWT. 

## Dynamic Resource Protection

This feature is available for the [`gluu-oauth-pep`](./gluu-oauth-auth-pep.md) and [`gluu-uma-pep`](./gluu-uma-auth-pep.md) plugins. 

![dynamic_path](../img/dynamic_path.png)

There are 3 elements to make more dynamic path registration and protection:

- ? match anyone path element
- ?? match zero or more path elements
- {regexp} - match single path element against PCRE

The priority for the elements are:

1. Exact match
1. Regexp match
1. ?
1. ??

!!! Info
    A `?` in the HTTP method allows all HTTP methods.

!!! Info
    You can use this elements to register more dynamic path for `required_acrs_expression` in [`gluu-openid-connect`](./gluu-openid-connect-uma-pep.md)

!!! Warning
    Multiple times `??` in path not supported.

### Examples

Assume that all paths below are registered in one plugin:

| Register Path | Apply security | Not Apply security |
|---------------|------------|-----------|
| `/??` | <ul><li>/folder/file.ext</li><li>/folder/file2</li><li>Apply security on all the paths</li></ul> | |
| `/folder/file.ext` | <ul><li>/folder/file.ext</li></ul> | <ul><li>/folder/file</li></ul> |
| `/folder/file` | <ul><li>/folder/file</li></ul> | <ul><li>/folder/file/</li><li>/folder/file/123</li><li>It will be good to use /?? wild card if you want to secure sub folders</li></ul> |
| `/folder/?/file` | <ul><li>/folder/123/file</li> <li>/folder/xxx/file</li></ul> | |
| `/path/??` | <ul><li>/path</li> <li>/path/</li> <li>/path/xxx</li> <li>/path/xxx/yyy/file</li></ul> | |
| `/path/??/image.jpg` | <ul><li>/path/one/two/image.jpg</li> <li>/path/image.jpg</li></ul> | |
| `/path/?/image.jpg` | <ul><li>/path/xxx/image.jpg - ? has higher priority than ??</li></ul> | |
| `/path/{abc|xyz}/image.jpg` | <ul><li>/path/abc/image.jpg</li> <li>/path/xyz/image.jpg</li></ul> | |
| `/users/?/{todos|photos}` | <ul><li>/users/123/todos</li> <li>/users/xxx/photos</li></ul> | |
| `/users/?/{todos|photos}/?` | <ul><li>/users/123/todos/</li> <li>/users/123/todos/321</li> <li>/users/123/photos/321</li></ul> | |

### Spontaneous scope

From version 4.2, Gluu Server supports OAuth2 [spontaneous scopes](https://gluu.org/docs/gluu-server/4.2/admin-guide/openid-connect/#spontaneous-scopes).


### OAuth2 client perspective

1. A client may register spontaneous scopes templates as regular expression: `^transaction:.+$`.
2. Later the client may request an access token with the scope which matches regexp above: `transaction:123asd`
3. Gluu Server matches the scope to the template and persists the scope with some configured TTL, issue the access token to the client.
4. Now client may use the access token to transaction-related requests to API.

### Spontaneous scope interception scripts

Gluu Server may be configured with spontaneous scope interception script, which may allow/reject spontaneous scope request. OAuth2 client should always check the response to be sure that all requested scopes returned.

### Gluu Gateway spontaneous scope extension

The gluu-oauth-pep plugin, which stands for Gluu OAuth2 Policy Enforcement Point, provides additional spontaneous scope related features.

In [scope expression](https://gluu.org/docs/gg/plugin/gluu-oauth-auth-pep/#oauth-scope-expression) instead of static scopes, we may use scope templates in PCRE regex notation. For example:

```JSON
{
  "path": "/posts/??",
  "conditions": [{
    "httpMethods": [
      "GET",
      "POST"
    ],
    "scope_expression": {
      "rule": {
        "and": [
          {
            "var": 0
          }
        ]
      },
      "data": [
        "^posts:(.+)$"
      ]
    }
  }]
}
```

In order to access paths with `/posts` prefix we need ANY scope which matches `^posts:(.+)$` regex.

### Scopes to URI mapping

The gluu-oauth-pep plugin is able to map the part(s) of request URI to spontaneous scopes. For this purposes, we introduce the Path Capture entity.

The variable parts of `protected path` may be referred to as `path captures`. 2 types of our wildcard/regexp protected path notation may catch path capture(s):

- `?` single path element
- `{<regexp)>}` - regexp path element. Regexp may contain **one or more PCRE capture group(s)**, otherwise the whole match is used as path capture.

We believe that multiple wildcard elements ?? is too wide and our implementation doesn't catch path capture here.

For example, the path /todos/?/command/{^(\d\d\d)-([a-d]{4})$} has 3 path captures. First ? catches one and {…} catches 2 others that is (\d\d\d) and ([a-d]{4}) which is inside (...). Copy and paste regexp expression inside {} to https://regex101.com/, you will get idea about capture groups.

Upon `/todos/hh/command/123-abcd` request the plugin catches 3 path captures:
1. `hh`
2. `123`
3. `abcd`

We may specify 3 spontaneous scopes templates in scope expression:
- `^todos:(.+)$`
- `^command:(\d\d\d)$`
- `^subcommand:([a-d]{4})$`

But we need some metadata on how to map path capture(s) to scope(s).

We require that spontaneous scope in scope expressions follow the convention:
1. Scope part which should match the path capture must be formed as PCRE named capturing group, `(?<group_name>...)` syntax is recommended.
2. The names of such groups should be `PC1`, `PC2`, ..., `PC9`. We support up to nine path capture per protected path.

Now we have enough metadata for mapping. For example:

```JSON
{
  "path": "/todos/?/command/{^(\\d\\d\\d)-([a-d]{4})$}",
  "conditions": [{
    "httpMethods": [
      "GET",
      "POST"
    ],
    "scope_expression": {
      "rule": {
        "and": [
          {
            "var": 0
          }
        ]
      },
      "data": [
        "^todos:(?<PC1>.+)$",
        "^command:(?<PC2>\\d\\d\\d)$",
        "^subcommand:(?<PC3>[a-d]{4})$",
        "^profile:.+$",
        "email"
      ]
    }
  }]
}
```

In order to access our API OAuth2 client needs to register spontaneous scopes:
- `^todos:.+$`
- `^command:\d\d\d$`
- `^subcommand:[a-d]{4}$`
-  `^profile:.+$`

You see that scopes to be registered are not obligated to contain capturing groups metadata. Gluu server has nothing to do with it. But for simplicity you may include it in registered scopes, Gluu server just ignores this:
- `^todos:(?<PC1>.+$`
- `^command:(?<PC2>\d\d\d)$`
- `^subcommand:(?<PC3>[a-d]{4})$`
-  "^profile:.+$`

Keep in mind that for different protected paths the same spontaneous scope may match a path capture with a different number, for example:

```JSON
[
  {
    "path": "/todos/?/command/{^(\\d\\d\\d)-([a-d]{4})$}",
    "conditions": [{
      "httpMethods": [
        "GET",
        "POST"
      ],
      "scope_expression": {
        "rule": {
          "and": [
            {
              "var": 0
            }
          ]
        },
        "data": [
          "^todos:(?<PC1>.+)$",
          "^command:(?<PC2>\\d\\d\\d)$",
          "^subcommand:(?<PC3>[a-d]{4})$",
          "^profile:.+$",
          "email"
        ]
      }
    }]
  },
  {
    "path": "/command/{^(\\d\\d\\d)-([a-d]{4})$}",
    "conditions": [{
      "httpMethods": [
        "GET",
        "POST"
      ],
      "scope_expression": {
        "rule": {
          "and": [
            {
              "var": 0
            }
          ]
        },
        "data": [
          "^command:(?<PC1>\\d\\d\\d)$",
          "^subcommand:(?<PC2>[a-d]{4})$"
        ]
      }
    }]
  }
]
```

So we recommend omitting named capturing groups metadata for Gluu server registration, It may reduce the number of registered scopes and avoid ambiguities.

### More examples

| Path |Total Path Capture| Spontaneous Scope |
|------|------------------|-------------------|
|`/posts/?/??`|<ol><li>`?`</li></ol>|`posts: (?<PC1>.+)$`|
|`/posts/?/image/?`|<ol><li>`?`</li><li>`?`</li></ol>|`posts: (?<PC1>.+)$` and `images: (?<PC2>.+)$`|
|`/users/??/?`|<ol><li>`?`</li></ol>|`posts: (?<PC1>.+)$`|
|`/images/?/{(.+)\.(jpg|png)}`|<ol><li>`?`</li><li>`(.+)`</li><li>`(jpg|png)`</li></ol>|`imgname: (?<PC1>.+)$`, `imgname: (?<PC2>.+)$` and `imgtype: (?<PC3>.+)$`|
|`/??`|<ul><li style="color:green">No capture group</li></ul>|<ul><li style="color:green">Plugin checks all registered scopes. You can use spontaneous scope here too.</li></ul>|
|`/comments/{\d\d\d}`|<ul><li style="color:green">No capture group</li></ul>|<ul><li style="color:green">Plugin use whole match. You can use spontaneous scope here too.</li></ul>|

## Custom Headers

After successful authentication, kong forward request upstream service. During this step, kong sends headers to your upstream headers. You can use this header to check and identify the user or request.

There is feature in all 3 authentication plugins `gluu-oauth-auth`, `gluu-uma-auth` and `gluu-openid-connect`. Every plugin provide an environment which you need to use to set headers. 

**For Example:** `gluu-oauth-auth` plugin has the `introspect_data` environment which has the token introspect response data. So If you want to set one header like `x-auth-token-exp` which will has the token expiration timestamp then you need to use the environment `introspect_data.exp` to set value.

Take a look on below table for available environment in every plugin

| Plugin | Environment |
|--------|-------------|
|`gluu-oauth-auth`|<ul><li>`consumer`</li><li>`introspect_data`</li></ul>|
|`gluu-uma-auth`|<ul><li>`consumer`</li><li>`introspect_data`</li></ul>|
|`gluu-openid-connect`|<ul><li>`id_token`</li><li>`userinfo`</li><li>`access_token`</li></ul>|

Every plugin has the `custom_headers` field. which is the the array and below is the structure of an object.

| Field | Description |
|-------|-------------|
|**header_name**|The title for header, may contain {*} placeholder when iterate thru claims, take a look at iterate field below. **Example:** http-kong-id-token|
|**value_lua_exp**|It is the lua expression which will populate the environment values. Note: For custom values you need to pass value in double quotes("value")|
|**format**|the format used to encode the value. **Formats:** string(as it is), base64, urlencoded, list, and jwt(with none alg)|
|**sep**|it is use when your header format is list type. It join the list of values by separator. **Example:** , (comma)|
|**iterate**|the header value should point to an environment values, for example id_token It use to iterate thru keys/values and create separate header for every key. The captured key is accessible in header name via special placeholder i.e. {*}.|

In UI, you will get facility to add custom headers. You just need to use header section. Click on `Add` Button to add header.

![oidc3-1](../img/oidc3-1.png)

### Example

1. `gluu-uma-auth`

     | Header Name | Value | Format | Separator | Iterate |
     |-------------|-------|--------|-----------|---------|
     |x-oauth-client-id|introspect_data|[JWT \| base64]||false|
     |x-consumer-id|consumer.id|[string \| base64 \| urlencoded]||false|
     |x-oauth-client-id|introspect_data.client_id|[string \| base64 \| urlencoded]||false|
     |x-rpt-expiration|introspect_data.exp|[string \| base64 \| urlencoded]||false|
     |x-oauth-token-{*}|introspect_data|[string \| urlencoded \| base64]||true|
     |kong-version|"version 2.0", <p style="color:green">Note: double quotes required for custom values.</p>|[string \| urlencoded \| base64]||false|

1. `gluu-oauth-auth`

     | Header Name | Value | Format | Separator | Iterate |
     |-------------|-------|--------|-----------|---------|
     |x-oauth-client-id|introspect_data|[JWT \| base64]||false|
     |x-consumer-id|consumer.id|[string \| base64 \| urlencoded]||false|
     |x-oauth-client-id|introspect_data.client_id|[string \| base64 \| urlencoded]||false|
     |x-rpt-expiration|introspect_data.exp|[string \| base64 \| urlencoded]||false|
     |x-oauth-token-{*}|introspect_data|[string \| urlencoded \| base64]||true|
     |kong-version|"version 2.0", <p style="color:green">Note: double quotes required for custom values.</p>|[string \| urlencoded \| base64]||false|
     |x-authenticated-scope|introspect_data.scope|list|, (comma)|No|

1. `gluu-openid-connect`

     | Header Name | Value | Format | Separator | Iterate |
     |-------------|-------|--------|-----------|---------|
     |kong-openidc-id-token|id_token|[string \| base64]||false|
     |kong-openidc-userinfo|userinfo|[jwt \| base64]|false|
     |kong-openidc-id-token-{*}|id_token|[string \| urlencoded \| base64]||true|
     |kong-openidc-userinfo-{*}|userinfo|[string \| urlencoded \| base64]||true|
     |kong-openidc-userinfo-email|userinfo.email|[string \| urlencoded \| base64]||false|
     |kong-openidc-id-token-exp|id_token.exp|[string \| base64]||false|
     |kong-userinfo-roles|userinfo.roles|[list]|, (comma)|false|
     |gg-access-token|access_token|[string \| urlencoded \| base64]||No|
     

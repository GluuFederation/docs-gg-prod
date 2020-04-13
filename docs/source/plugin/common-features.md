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

Gluu Gateway and Gluu Server are now supports spontaneous scope. Sometime their is need for dynamic scope as per the request.
 
**For example:** `/transactions/45` and `transactions/46`, you want to issue two diff scope `read: 45` and `read: 46` like wise for every dynamic protected resources, In this case spontaneous scope will help you. where you can issue the dynamic scope.

Plugin supports the [PCRE regexp](https://en.wikipedia.org/wiki/Perl_Compatible_Regular_Expressions) for dynamic path and spontaneous scope registration. To map dynamic path value with scope, we recommended to use the `PCRE Capture Group names` for spontaneous scope registration.

Usually we need that `spontaneous scope` match some path element, but it is not mandatory. The variable part of `protected path` may be detected as `path capture`. 2 types of our wildcard/regexp notation may catch path capture(s):

- `?` single path element
- `{<regexp)>}` - regexp path element. Regexp may contain **one or more PCRE capture group(s)**, otherwise the whole match is used as path capture.

!!! Warning
    Allowing multiple wildcard elements **??** is considered too wide and cannot be used as path capture.    

**For example:** 

The path `/posts/?/image/?` has two `path captures`. First `?` and Second `?`. then your spontaneous scope will be like `posts: (?P<PC1>.+)$` and `image: (?P<PC2>.+)$`. Here the `PC1` and `PC2` is the `Named Capture Group`. `PC` stand for `Path Capture`.  You can say the variable in the expression. It is just a name, you can give any name. Copy this expression `(?P<PC1>.+)$` and put it in online regexp tool [https://regex101.com](https://regex101.com). You will get idea how `PCRE Capture Group names` works.

So `posts: (?P<PC1>.+)$` scope try to match with the first path capture `?` value and `posts: (?P<PC2>.+)$` scope try to match with the second path capture `?` value. 

The real request path example `/posts/123/images/ps.jpg`. In order to access this page, the AT should be authorized with `posts:123abc` abd `images: 321cba` scope.

#### OAuth Spontaneous scope workflow

1. Register `spontaneous scope` in OAuth client for example: `posts: (?P<PC1>.+)$`.
1. Request for spontaneous scope for example: `posts: 1234` which match the `spontaneous scope` regexp.
1. Gluu CE persists `user: 1234` with some TLL.
1. GG may introspect the Access Token and see a valid scope i.e. `user: 1234`.
1. `gluu-oauth-pep` plugin  may be provisioned to match not only to fixed scopes, but also against SS regexp.

### Examples

| Path |Total Path Capture| Spontaneous Scope |
|------|------------------|-------------------|
|`/posts/?`|<ol><li>`?`</li></ol>|`posts: (?P<PC1>.+)$`|
|`/posts/?/image/?`|<ol><li>`?`</li><li>`?`</li></ol>|`posts: (?P<PC1>.+)$` and `images: (?P<PC2>.+)$`|
|`/users/??/?`|<ol><li>`?`</li></ol>|`posts: (?P<PC1>.+)$`|
|`/images/?/{(.+)\.(jpg|png)}`|<ol><li>`?`</li><li>`(.+)`</li><li>`(jpg|png)`</li></ol>|`imgname: (?P<PC1>.+)$`, `imgname: (?P<PC2>.+)$` and `imgtype: (?P<PC3>.+)$`|

The syntax of a PCRE is too rich, and in general it's not possible to build a match from a PCRE and some known named groups. We added some SS regexp limitations, for example:

1. Only named capturing groups are allowed
1. Only named capturing groups may contain variable characters parts
1. Accepted capturing group names are from PC1 to PC9, it should be enough, one single digit simplify parsing a lot
1. Capturing groups shouldn't be nested and recursive
1. All special characters outside capturing groups should be escaped in accordance with PCRE rules

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
     

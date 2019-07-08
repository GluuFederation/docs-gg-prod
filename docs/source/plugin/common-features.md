# Common Features

Below are the features which is common in Gluu OAuth and UMA plugins.

## Phantom Token

In some cases there is requirement that bearer token for outside of the network and JWT token for the internal network.

![phantom_token](../img/phantom_token.png)
 
This feature is available in both plugins that is [`gluu-oauth-auth`](../gluu-oauth-auth-pep) and [`gluu-uma-auth`](../gluu-uma-auth-pep). To configure phantom token feature, you just need to set `pass_credentials='phantom_token'` in plugin configuration. 

!!! Important
    Set `access_token_as_jwt: false` and `rpt_as_jwt: false` in [client registration](../../admin-gui/#consumers), otherwise client by default returns you access token as JWT. 

## Dynamic Resource Protection

This feature is available for both plugins that is [`gluu-oauth-pep`](../gluu-oauth-auth-pep) and [`gluu-uma-pep`](../gluu-uma-auth-pep). 

![dynamic_path](../img/dynamic_path.png)

There are 3 elements to make more dynamic path registration and protection:

- ? match any one path element
- ?? match zero or more path elements
- {regexp} - match single path element against PCRE

The priority for the elements are:

1. Exact match
1. Regexp match
1. ?
1. ??

!!! Note
    slash(/) is required before multiple wildcards placeholder.
    
Examples: 

Assume that below all path is register in one plugin

| Register Path | Allow path | Deny path |
|---------------|------------|-----------|
| `/folder/file.ext` | <ul><li>/folder/file.ext</li></ul> | <ul><li>/folder/file</li></ul> |
| `/folder/?/file` | <ul><li>/folder/123/file</li> <li>/folder/xxx/file</li></ul> | |
| `/path/??` | <ul><li>/path/</li> <li>/path/xxx</li> <li>/path/xxx/yyy/file</li></ul> | <ul><li>/path - Need slash at last</li></ul> |
| `/path/??/image.jpg` | <ul><li>/path/one/two/image.jpg</li> <li>/path/image.jpg</li></ul> | |
| `/path/?/image.jpg` | <ul><li>/path/xxx/image.jpg - ? has higher priority than ??</li></ul> | |
| `/path/{abc|xyz}/image.jpg` | <ul><li>/path/abc/image.jpg</li> <li>/path/xyz/image.jpg</li></ul> | |
| `/users/?/{todos|photos}` | <ul><li>/users/123/todos</li> <li>/users/xxx/photos</li></ul> | |
| `/users/?/{todos|photos}/?` | <ul><li>/users/123/todos/</li> <li>/users/123/todos/321</li> <li>/users/123/photos/321</li></ul> | |

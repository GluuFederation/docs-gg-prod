# Architecture

This diagram illustrates the architecture of Gluu Gateway and some of its components:

![gg-architecture.png](img/gg-architecture.png)

Below is the details description of the above diagram:

### Point 1: Admin UI

The first step is configurations and added security plugins. Gluu Gateway(GG) will provide you [`Admin UI`](admin-gui.md) on the port `:1338`. You can use this UI to add your API/Web application(Upstream Service/API/Web) with `kong service object`, `kong route object`, `create OpenID Connect client`, `kong consumer object` and configure the `plugins`. 

The Kong Service is the object where you need to register Upstream Service. Check [plugins](/plugin/gluu-openid-connect-uma-pep/) and [tutorial](/tutorials/oidc-steppedup-auth-tutorial/) section for UI configurations.

### Point 2: Security configuration using UI

UI uses the [Kong Admin APIs](https://docs.konghq.com/1.3.x/admin-api/) to configure the Kong's Services, Routes, Consumers and Plugins.

### Point 3: Security configuration using Kong API

You can directly use the [Kong Admin APIs](https://docs.konghq.com/1.3.x/admin-api/) to configure the Kong's Services, Routes, Consumers and Plugins. You can find APIs description in [Kong Docs](https://docs.konghq.com/1.3.x/admin-api/) and [GG plugins docs](/plugin/gluu-openid-connect-uma-pep/) for Gluu plugins configuration API.

### Point 4: UI uses OXD Server to manage OP Client

UI uses the [OXD Server](https://gluu.org/docs/oxd/4.0/) endpoint during plugin configuration to create and manage the OpenID Connect Client.

### Point 5: Upstream API/Web Application registration

Upstream Service is your Rest API/Web application which you want to protect using Kong and plugins. We already explain this point in `Point 1`. The Kong Service is the object where you need to register Upstream Service. Check [plugins](/plugin/gluu-openid-connect-uma-pep/) and [tutorial](/tutorials/oidc-steppedup-auth-tutorial/) section for UI configurations.

### Point 6: OpenID Connect Server configuration

This is the last step of configuration. UI creates the OP Client which you can manage and see using the Gluu Server UI(oxTrust).

!!! Important
    Do not update client using OP server, always use the OXD Server's `/update-site` endpoint to update the client because GG uses the OXD Server to deal with client and OP Server.
    
### Point 7: Client Application request for Token using OXD-Server

Client application is the users of your application which request to your application endpoint(Kong proxy endpoint :443). Admin has to give the OP Client's `client_id` and `client_secret` to the client application, so that the client application use this credential to get the OAuth token and request for the access of registered resources in GG.

Client application can use the OXD-Server endpoint to get the token. In this case, its need to use the [OXD Server endpoints](https://gluu.org/docs/oxd/4.0/api/).

### Point 8: Client Application request for Token directly to OP server endpoints

This is same as above but in this case client application directly communicate to OP server using `client_id` and `client_secret` to get tokens.

### Point 9: Client Application request to protected resources

Now client application has the token. It will request to kong-proxy endpoint with the token in `authorization` header. 

### Point 10: Kong execute the configured plugins

Now the control goes to Kong. Kong execute all the configured plugins and uses the OXD-Server to validate the token with the OP Server. In `OAuth Plugin` case, Plugin uses the [`/introspect-access-token`](https://gluu.org/docs/oxd/4.0/api/#introspect-access-token) endpoint to validate the token.

### Point 11: OXD Server request to OP Server

OXD-Server request to OP server for authentication and authorization. For Example, In `OAuth Plugin` case, OXD-Server request to OP Server introspect endpoint to validate the token.

### Point 12: Send Upstream response to Client Application

After successful client authentication and authentication, Kong send upstream response to client application.


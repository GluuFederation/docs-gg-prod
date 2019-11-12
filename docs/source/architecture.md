# Architecture

This diagram illustrates the architecture of Gluu Gateway and some of its components:

![gg-architecture.png](img/gg-architecture.png)

The following sections explain the numbered points in the diagram:

### 1. Admin UI

The first step is configuration and added security plugins. Gluu Gateway (GG) will provide the [`Admin UI`](./admin-gui.md) on port `:1338`. Use this UI to add your API or Web application (such as Upstream Service/API/Web) with `kong service object`, `kong route object`, `create OpenID Connect client`, `kong consumer object` and configure the `plugins`. 

The Kong Service is the object where you need to register Upstream Service. Check [Services](./admin-gui.md#services) and [Routes](./admin-gui.md#routes) section.

### 2. Security configuration using UI

UI uses the [Kong Admin APIs](https://docs.konghq.com/1.3.x/admin-api/) to configure the Kong's Services, Routes, Consumers and Plugins.

### 3. Security configuration using Kong API

You can directly use the [Kong Admin APIs](https://docs.konghq.com/1.3.x/admin-api/) to configure the Kong's Services, Routes, Consumers and Plugins. You can find APIs description in [Kong Docs](https://docs.konghq.com/1.3.x/admin-api/) and [GG plugins docs](./plugin/gluu-openid-connect-uma-pep.md) for Gluu plugins configuration API.

### 4. UI uses the oxd Server to manage OP Client

UI uses the [oxd Server](https://gluu.org/docs/oxd/4.0/) endpoint during plugin configuration to create and manage the OpenID Connect Client.

### 5. Upstream API/Web Application registration

Upstream Service is your Rest API/Web application which you want to protect using Kong and plugins. We already explain this point in `Point 1`. The Kong Service is the object where you need to register Upstream Application. You can register N number of upstream application. As you can see in diagram, there is 3 different upstream applications registered in kong. Upstream Apps should be locally hosted and do not open it for public. You need to open the kong proxy endpoint for your end-users or client applications. Check [Services](./admin-gui.md#services) and [Routes](./admin-gui.md#routes) section for upstream application registration in kong.

After registering upstream app as a services/routes in kong, next step is to add security plugins. Check [plugins](./plugin/gluu-openid-connect-uma-pep.md) and [tutorial](./tutorials/oidc-steppedup-auth-tutorial.md) section for plugins details and configurations.

### 6. OpenID Connect Server configuration

This is the last configuration step. The UI creates the OP Client, which can be managed using oxTrust, the Gluu Server's UI.

!!! Important
    Do not update the client using the OP server, always use the oxd server's `/update-site` endpoint to update the client, since GG uses the oxd server with both the client and OP server.
    
### 7. Client Application requests a token using the oxd server

The client application sends a request to the application endpoint (i.e. the Kong proxy endpoint :443). The admin has to provide the OP Client's `client_id` and `client_secret` to the client application for it to use these credentials to get the OAuth token and send a request to access registered resources in GG.

The client application can use the oxd server endpoint to get the token. In this case, it needs to use the [oxd server endpoints](https://gluu.org/docs/oxd/4.0/api/).

### 8. Client Application request for Token directly to OP server endpoints

This is same as the previous step, but this time the client application directly communicates with the OP server using `client_id` and `client_secret` to get tokens.

### 9. Client Application request to protected resources

Now the token is with the client application. It will send a request to the Kong proxy endpoint with the token in the `authorization` header. 

### 10. Kong execute the configured plugins

At this point, Kong executes all configured plugins and uses the oxd server to validate the token with the OP Server. Using the `OAuth Plugin`, for example, the plugin uses the [`/introspect-access-token`](https://gluu.org/docs/oxd/4.0/api/#introspect-access-token) endpoint to validate the token.

### 11. oxd server sends a request to the OP Server

The oxd server sends a request to the OP server for authentication and authorization. Using the `OAuth Plugin`, for example, the oxd server sends a request to the OP server's introspection endpoint to validate the token.

### 12. Send Upstream response to Client Application

After successful client authentication and authentication, Kong sends an upstream response to the client application.

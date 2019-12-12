# Gluu Gateway 4.0.x

!!! Attention
    This documentation is being restructured. There may be temporary broken links or formatting errors. Thank you for your patience while we improve our docs!

## Overview

Gluu Gateway (GG) is an authentication and authorization solution for APIs and websites. 

GG bundles the the open source [Kong Gateway](https://konghq.com/community/) for its core functionality, and adds a GUI and custom plugins to enable access management policy enforcement using OAuth, UMA, OpenID Connect and Open Policy Agent (OPA). In addition, GG supports the broader ecosystem of [Kong plugins](https://docs.konghq.com/hub/) to enable API rate limiting, logging, and many other capabilities.

## Features

Primary use cases supported by Gluu Gateway include:

- Authentication by OAuth2 / UMA bearer tokens and JWT
- Authorization by OAuth2 / UMA scopes
- Proxy websites, acting as an OpenID Connect Relying Party (RP)
- Control access to API's by delegating policy decisions to an Open Policy Agent (OPA) server 
- Rate limiting, logging, and many other capabilities enabled via the ecosystem of Kong plugins 

## Terminology

- **Upstream Service/Application**: The upstream application can be a REST API or Web Application. This is the application secured by Gluu Gateway.   

- **Gateway or Proxy:** The gateway is proxy software that acts as middleware between client applications and upstream applications. It provides very the powerful ability to add and execute code to filter requests and and add a layer of security. If the request is approved, the gateway takes the response from the upstream application and returns it to the client applications. Gluu Gateway uses Kong for this purpose. 

- **Kong Services:** The Kong Admin API entity that represents an upstream application.

- **Kong Routes:** The Kong Admin API entity that represents a way to map downstream requests to upstream services. It's used to filter the request and send it to the appropriate service at the upstream application.

     ```
     Request --> Routes --> Service --> Upstream Application
     ```
 
- **Consumer:** The Admin API entity that represents a developer or machine using the API. The Consumer only communicates with Kong, which then proxies all calls to the appropriate Upstream Service. In both `gluu-oauth-auth` and `gluu-uma-auth` plugins, the OP client is validated by checking the Consumer entity.
  
     In Gluu Gateway, an `OpenID Connect Client's client_id` is associated with a `Consumer` in Kong. This is useful where access control is restricted to certain clients. All other default Kong client authentication plugins are disabled -- we only want to use the [Gluu Server](https://gluu.org/docs/ce) for client authentication. The `client_id` is verified by looking at the JWT or introspection response. 

## Components

Gluu Gateway includes the following components.

- [Kong CE v1.3.x](https://konghq.com/community/): An open source API Gateway and Microservices Management Layer, delivering high performance and reliability. We are using Kong Community edition version 1.3 here. 

- [Gluu Admin UI](https://github.com/GluuFederation/gluu-gateway/tree/version_4.0/konga): A web administration portal, based on the [Konga](https://github.com/pantsel/konga) GUI, to manage your Gluu Gateway configurations.

- [Gluu Gateway plugins](https://github.com/GluuFederation/gluu-gateway/tree/version_4.0/kong/plugins): Plugins that leverage the Gluu Server for central client management and to control access to upstream APIs using OAuth 2.0 and UMA 2.0.

- [oxd Server v4.0](https://www.gluu.org/docs/oxd/4.0): Middleware server for OpenID, OAuth, and UMA client communication with an associated OAuth Authorization Server, typically an instance of the Gluu Server. Gluu-gateway uses oxd server endpoints to communicate with OP Server.

- Others: The following runtime environment is required by the Gluu Gateway package:
    - OpenJDK v8
    - Python v2.x
    - Postgres v10
    - Node v8.9.4
    - NPM v5.6.0
    
## Dependencies 

- To support OAuth2, UMA and OpenID Connect use cases, GG requires an external [Gluu Server Authorization Server](https://gluu.org/). 

- To support OPA use cases, GG requires an external [OPA Policy Engine](https://gluu.org/). 

## Access Control

Gluu Gateway functions as a policy enforcement point ("PEP"), relying on an external policy decision point ("PDP"). Gluu Gateway has two primary jobs:

1. Validate tokens (both value and reference tokens)
1. Make sure a request is authorized

Gluu Gateway supports both UMA and OAuth tokens. While mechanically the same, scopes have different meanings in UMA and OAuth.

### UMA Authentication and scopes security
UMA scopes represent centralized policy evaluation. For example, in the Gluu Server, administrators can map UMA scopes to policies, expressed as [UMA RPT Authorization interception scripts](https://gluu.org/docs/ce/admin-guide/custom-script/#uma-2-rpt-authorization-policies).

![UMA PEP diagram](img/gluu-uma-pep.png)

### OAuth Authentication and scopes security
OAuth scopes normally represent a user's authorization, for example authorization to access a user's calendar. How scopes are granted is ultimately up to the Authorization Server that issues it.

![OAuth PEP diagram](img/gluu-oauth-pep.png)

### Open Policy Agent based policies

In the Gluu Server, additional claims can be included in an access token. Gluu Gateway can forward this access token to an Open Policy Agent (OPA) server for a policy decision.

### OIDC and UMA with Claim gathering Security
OpenID is an identity layer. The Gluu Gateway can act as an OpenID Relying Party, redirecting the subject's browser to the OpenID Provider, and obtaining user information. At that point, the Gluu Gateway can attempt to obtain an UMA access token, pushing the OpenID Connect id_token to the UMA RPT endpoint as input to the policy decision. If user interaction is required, the Gluu Gateway may redirect the subject to the UMA Authorization endpoint.  

!!! Note
    An API that uses OAuth for security can only be called by OAuth clients, and likewise, an API that uses UMA for security can only be called by an UMA client.

### Clients vs. Consumers
In Gluu Gateway, a `client_id` is associated with a "Consumer" in Kong. This is useful where access control is restricted to certain clients. All other forms of client authentication are disabled in the Gluu Gateway Admin GUI -- we just want to use an OAuth Authorization Server like the Gluu Server for client authentication. The Gluu Server plugins verify the `client_id` for which a token was issued by looking at the JSON equivalent (either the JWT or the introspection response).

## Architecture

This diagram illustrates the architecture of Gluu Gateway and some of its components:

![gg-architecture.png](img/gg-architecture.png)

The following sections explain the numbered points in the diagram:

1. Admin UI
    The first step is setting up configuration and adding security plugins. Gluu Gateway (GG) will provide its [`Admin UI`](./admin-gui.md) on port `:1338`. Use this UI to add your API or Web application (such as Upstream Service/API/Web) with `kong service object`, `kong route object`, `create OpenID Connect client`, `kong consumer object` and configure the `plugins`. 
    The Upstream Service should be registered with the Kong Service. See the [Services](./admin-gui.md#services) and [Routes](./admin-gui.md#routes) sections.

1. Security configuration using UI
    The GG UI uses the [Kong Admin APIs](https://docs.konghq.com/1.3.x/admin-api/) to configure Kong's Services, Routes, Consumers and Plugins.

1. Security configuration using Kong API
    Instead, you can directly use the [Kong Admin APIs](https://docs.konghq.com/1.3.x/admin-api/) to configure the Kong's Services, Routes, Consumers and Plugins. You can find API descriptions in the [Kong Docs](https://docs.konghq.com/1.3.x/admin-api/) and [GG plugins docs](./plugin/gluu-openid-connect-uma-pep.md) for Gluu plugins configuration API.

1. The UI uses the oxd server to manage the OP Client
    The UI uses the [oxd server](https://gluu.org/docs/oxd/4.0/) endpoint during plugin configuration to create and manage the OpenID Connect Client.

1. Upstream API/Web Application registration
    The Upstream Service is the Rest API/Web application to protect using Kong and the plugins, as discussed in point 1, above. The Kong Service is the object where the Upstream Application is registered. You can register multiple upstream applications. As shown in diagram, there are three different upstream applications registered in Kong. Upstream Apps should be locally hosted and not publically accessible. However, the Kong proxy endpoint should be opened for end-users or client applications. Check the [Services](./admin-gui.md#services) and [Routes](./admin-gui.md#routes) docs for upstream application registration in Kong.
     After registering an upstream app as a service or route in Kong, the next step is to add security plugins. Check the [plugins](./plugin/gluu-openid-connect-uma-pep.md) and [tutorial](./tutorials/oidc-steppedup-auth-tutorial.md) docs for plugin details and configuration.

1. OpenID Connect Server configuration
    This is the last configuration step. The UI creates the OP Client, which can be managed using oxTrust, the Gluu Server UI.
    Do not update the client using the OP server, always use the oxd server's `/update-site` endpoint to update the client, since GG uses the oxd server with both the client and OP server.
    
1. Client Application requests a token using the oxd server
    The client application sends a request to the application endpoint (i.e. the Kong proxy endpoint :443). The admin has to provide the OP Client's `client_id` and `client_secret` to the client application for it to use these credentials to get the OAuth token and send a request to access registered resources in GG.
    The client application can use the oxd server endpoint to get the token. In this case, it needs to use the [oxd server endpoints](https://gluu.org/docs/oxd/4.0/api/).

1. Client Application request for Token directly to OP server endpoints
    This is same as the previous step, but this time the client application directly communicates with the OP server using `client_id` and `client_secret` to get tokens.

1. Client Application request to protected resources
    Now the token is with the client application. It will send a request to the Kong proxy endpoint with the token in the `authorization` header. 

1. Kong executes the configured plugins
    At this point, Kong executes all configured plugins and uses the oxd server to validate the token with the OP Server. Using the `OAuth Plugin`, for example, the plugin uses the [`/introspect-access-token`](https://gluu.org/docs/oxd/4.0/api/#introspect-access-token) endpoint to validate the token.

1. The oxd server sends a request to the OP Server
    The oxd server sends a request to the OP server for authentication and authorization. Using the `OAuth Plugin`, for example, the oxd server sends a request to the OP server's introspection endpoint to validate the token.

1. Send Upstream response to Client Application
    After successful client authentication and authentication, Kong sends an upstream response to the client application.


## License  

Gluu Gateway is available under the [GLUU SUPPORT license](https://raw.githubusercontent.com/GluuFederation/gluu-gateway/master/LICENSE).

# Components

## Gluu Gateway
Gluu Gateway bundles the following software components:

- [Kong CE v1.3](https://konghq.com/community/): An open source API Gateway and Microservices Management Layer, delivering high performance and reliability.

- [Gluu Konga Admin GUI](https://github.com/GluuFederation/gluu-gateway/tree/version_4.0/konga): A web administration portal, based on the [Konga](https://github.com/pantsel/konga) GUI, to manage your Gluu Gateway.

- [Gluu Gateway plugins](https://github.com/GluuFederation/gluu-gateway/tree/version_4.0/kong/plugins): Plugins that leverage the Gluu Server for central client management and to control access to upstream APIs using OAuth 4.0 and UMA 4.0.

- [oxd-Server v4.0](https://www.gluu.org/docs/oxd/4.0): Middleware server for OpenID, OAuth, and UMA client communication with an associated OAuth Authorization Server, typically an instance of the Gluu Server (details [below](#gluu-server-pre-requirements)).

- Others: The following runtime environment is required by the Gluu Gateway package:
    - OpenJDK v8
    - Python v2.x
    - Postgres v10
    - Node v8.9.4
    - NPM v5.6.0

## Gluu Server pre-requirements

Gluu Gateway requires an OAuth 2.0 Authorization Server (AS), typically the Gluu Server, to issue OAuth clients and scopes, and perform client authentication.

Gluu Gateway is compatible with the following versions of Gluu:

- Gluu Server [CE 4.0](https://gluu.org/docs/ce/4.0)

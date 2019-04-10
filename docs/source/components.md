# Components

## Gluu Gateway
Gluu Gateway bundles the following software components:

- [Kong CE v0.14](https://konghq.com/community/): An open source API Gateway and Microservices Management Layer, delivering high performance and reliability.

- [Gluu Konga Admin GUI](https://github.com/GluuFederation/gluu-gateway/tree/version_1.0.1/konga): A web administration portal, based on the [Konga](https://github.com/pantsel/konga) GUI, to manage your Gluu Gateway.

- [Gluu Gateway plugins](https://github.com/GluuFederation/gluu-gateway): Plugins that leverage the Gluu Server for central client management and to control access to upstream APIs using OAuth 2.0 and UMA 2.0.

- [oxd-Server v4.0](https://www.gluu.org/docs/oxd/4.0): Gluu's client middleware server for OpenID, OAuth, and UMA client communication.

- Others: The following runtime environment is required by the Gluu Gateway package:
    - OpenJDK v8
    - Python v2.x
    - Postgres v10
    - Node v8.9.4
    - NPM v5.6.0

## Gluu Server pre-requirements

Gluu Gateway requires an OAuth 2.0 Authorization Server (AS), typically the Gluu Server, to issue OAuth clients and scopes, and perform client authentication.

Gluu Gateway is compatible with the following versions of Gluu:

- Gluu Server [CE 3.1.6](https://gluu.org/docs/ce/3.1.6)

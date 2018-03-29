# Gluu Gateway

## Overview

The Gluu Gateway is a API Gateway which can be used to quickly deploy an OAuth 2.0 and User-Managed Access (UMA) 2.0 Grant protected API gateway.

## Components

Gluu Gateway uses the following components:

1. **[Gluu Server v3.1.3](https://gluu.org):** free open source software package for identity and access management. You should need to use Gluu server version >= 3.1.3.
1. **[OXD-Server v3.1.3](https://oxd.gluu.org):** OpenID Connect and UMA middleware service used for client credential management and cryptographic validation. 
1. **[Kong v0.11.x](https://getkong.org):** The open-source API Gateway and Micro services Management Layer, delivering high performance and reliability.
1. **[Gluu Kong plugins]()**: Use the Gluu Server to control access to upstream API's using OAuth 2.0 clients and UMA 2.0.
1. **[Admin GUI Portal - Konga]()**: A web administration portal, based on [Konga](https://github.com/pantsel/konga) GUI, that makes it easier to manage your Gluu Gateway.
1. **Others**: Following runtime environment require by Gluu Gateway package. 
    - OpenJDK v8
    - Python v2.x
    - Postgres v10
    - Node v8.9.4
    - NPM v5.6.0

## Features

1. Manage Kong Admin API, Consumer and plugin objects.
1. Configure User-Managed Access (UMA) 2.0 Grant for OAuth 2.0 Authorization to register API objects.
1. API Dashboard to configure and monitor the health of your servers.
1. Backup, restore and migrate Kong instances using snapshots.
1. Leverages the security and upgradability of the oxd-server.

## Get Started

Use the following links to get started with credential manager:  

1. [Installation](./installation.md)
1. [Configuration](./configuration.md)
1. [Admin GUI Portal - Konga](./admin-gui-portal.md)
1. [Admin API](./admin-api.md)
1. [FAQ](./faq.md)

## License

Gluu Gateway is licensed under the free open source [MIT License](https://github.com/GluuFederation/gluu-gateway/blob/master/LICENSE).
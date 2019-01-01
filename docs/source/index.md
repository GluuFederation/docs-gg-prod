# Gluu Gateway 4.0.Beta

## Overview

Gluu Gateway is an API Gateway that leverages the open source [Gluu Server](https://gluu.org/) for central client management and access control, and inherits core gateway functionality from the open source [Kong API Gateway](https://konghq.com/kong-community-edition/). 

## Features
Gluu Gateway adds the following functionality to the Kong API Gateway:

- Leverage Gluu's OAuth 2.0 authorization server for central client authentication.
- Control access to APIs using OAuth and UMA scopes.
- Access Token as JWT feature.
- Expose client authnetication and grant metrics.
- GUI to manage Kong Service, Route, Consumer and Plugin objects.
- API Dashboard to configure and monitor the health of your servers.
- Backup, restore and migrate Kong instances using snapshots.

## Access Control
Gluu Gateway enables API access management via Gluu OAuth PEP (policy enforcement point) and UMA PEP plugins.

### OAuth PEP
The OAuth PEP plugin provides client security by authenticating OAuth token and token scope security.

![OAuth PEP diagram](img/diagram-oauth-mode.jpg)

### UMA PEP
The UMA PEP plugin provides client security by authenticating RPT token and UMA scope security.

![UMA PEP diagram](img/diagram-uma-mode.jpg)

## Components

Gluu Gateway makes use of the following software components:

- [Kong v0.14](https://getkong.org): An open source API Gateway and Microservices Management Layer, delivering high performance and reliability.

- [Gluu Konga Admin GUI](https://github.com/GluuFederation/gluu-gateway/tree/master/konga): A web administration portal, based on the [Konga](https://github.com/pantsel/konga) GUI, to manage your Gluu Gateway.

- [Gluu Gateway plugins](https://github.com/GluuFederation/gluu-gateway): Plugins that leverage the Gluu Server for central client management and to control access to upstream APIs using OAuth 2.0 and UMA 2.0.

- [oxd-Server v4.0.Beta](https://oxd.gluu.org): An OpenID Connect and UMA middleware service used to enable client credential management and cryptographic validation against the Gluu Server.

- Others: The following runtime environment is required by the Gluu Gateway package: 
    - OpenJDK v8
    - Python v2.x
    - Postgres v10
    - Node v8.9.4
    - NPM v5.6.0'
    
## Get Started

Use the following links to get started:  

1. [Installation](./installation.md)
1. [Configuration](./configuration.md)
1. [Admin GUI](./admin-gui.md)
1. Plugins
    1. [Gluu OAuth PEP](./plugin/gluu-oauth-pep.md)
    2. [Gluu UMA PEP](./plugin/gluu-uma-pep.md)
    3. [Gluu Metrics](./plugin/gluu-metrics.md)
1. [FAQ](./faq.md)

## Licenses

Gluu Gateway leverages software written by Gluu and incorporated from other projects. The license for each software component is listed below.

| Component | License |
|-----------|---------|
| Kong API Gateway | [Apache2]( http://www.apache.org/licenses/LICENSE-2.0) |
| Konga GUI | [MIT License](http://opensource.org/licenses/MIT) |
| oxd-Server | [Apache2]( http://www.apache.org/licenses/LICENSE-2.0) |
| Gluu Gateway | [Gluu Stepped-Up Support License](https://github.com/GluuFederation/gluu-gateway/blob/master/LICENSE) |

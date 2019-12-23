# Gluu Gateway 1.0.x

## Overview

Gluu Gateway enables organizations to restrict access to APIs by requiring OAuth clients to present valid access tokens issued by an OAuth or UMA Authorization Server (AS), typically a [Gluu Server](https://gluu.org/docs/ce).

Gluu Gateway is built on top of the [Kong Community Edition (CE) API gateway](https://konghq.com/community/) and leverages the strong ecosystem of Kong plugins to enable rate limiting, logging, and many other capabilities. 

## Features

Primary use cases and functionalities supported by Gluu Gateway include:

- Control access to APIs by requiring OAuth or UMA scopes
- Collect and report OAuth and UMA usage metrics
- Introspect bearer access tokens
- Validate the signature of JWT access tokens
- A GUI to simplify ongoing administration
- Support for Kong plugins to enable rate limiting, logging, and many other capabilities  

## Access Control

Gluu Gateway functions as a policy enforcement point ("PEP"), relying on an external policy decision point ("PDP")--in most cases, the Gluu Server--for policy evaluation. Gluu Gateway has two primary jobs: 

1. Make sure a token is active; 
1. Make sure a token has the correct scopes to call the endpoint it is requesting.

Gluu Gateway supports both UMA and OAuth tokens. While mechanically the same, scopes have different meanings in UMA and OAuth: 

### UMA scopes
UMA scopes represent centralized policy evaluation. For example, in the Gluu Server, administrators can map UMA scopes to policies, expressed as [UMA RPT Authorization interception scripts](https://gluu.org/docs/ce/admin-guide/custom-script/#uma-2-rpt-authorization-policies). 

![UMA PEP diagram](img/gluu-uma-pep.png)

### OAuth scopes
OAuth scopes normally represent a user's authorization, for example authorization to access a user's calendar. How scopes are granted is ultimately up to the Authorization Server that issues it.

![OAuth PEP diagram](img/gluu-oauth-pep.png)

!!! Note 
    An API that uses OAuth for security can only be called by OAuth clients, and likewise, an API that uses UMA for security can only be called by an UMA client. 

### Clients
In Gluu Gateway, a `client_id` is associated with a "Consumer" in Kong. This is useful where access control is restricted to certain clients. All other forms of client authentication are disabled in the Gluu Gateway Admin GUI -- we just want to use an OAuth Authorization Server like the Gluu Server for client authentication. The Gluu Server plugins verify the `client_id` for which a token was issued by looking at the JSON equivalent (either the JWT or the introspection response).

## Getting Started

Use the following links to get started:  

1. [Installation](./installation.md)
1. [Configuration](./configuration.md)
1. [Admin GUI](./admin-gui.md)
1. Plugins
    1. [Gluu OAuth PEP](./plugin/gluu-oauth-pep.md)
    2. [Gluu UMA PEP](./plugin/gluu-uma-pep.md)
    3. [Gluu Metrics](./plugin/gluu-metrics.md)
1. [FAQ](./faq.md)

## License  

Gluu Gateway is available under the [Apache 2.0 license](https://raw.githubusercontent.com/GluuFederation/gluu-gateway/master/LICENSE). 

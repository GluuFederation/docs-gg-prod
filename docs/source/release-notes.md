## Notice

This document, also known as the Gluu Gateway Release Note, relates to the Gluu Gateway Release versioned 4.2.x. The work is licensed under the “[The Apache 2.0](https://www.apache.org/licenses/LICENSE-2.0) License” allowing the use, copy, modify, merge, publish, distribute, sub-license and sale without limitation and liability, provided the end-user person or organization using this software has an active support subscription for this software with either Gluu or one of Gluu's OEM partners after using the software for more than 30 days. This document extends only to the aforementioned release version in the heading.  

UNLESS IT HAS BEEN EXPRESSLY AGREED UPON BY ANY WRITTEN AGREEMENT BEFOREHAND, THE WORK/RELEASE IS PROVIDED “AS IS”, WITHOUT ANY WARRANTY OR GUARANTEE OF ANY KIND EXPRESS OR IMPLIED. UNDER NO CIRCUMSTANCE, THE AUTHOR, OR GLUU SHALL BE LIABLE FOR ANY CLAIMS OR DAMAGES CAUSED DIRECTLY OR INDIRECTLY TO ANY PROPERTY OR LIFE WHILE INSTALLING OR USING THE RELEASE.  

## Purpose

The document is released with Version 4.2.x of the Gluu Gateway Software. The purpose of this document is list changes made and new features included in this release. The list is not exhaustive and there might be some omission of negligible issues, but the noteworthy features, enhancements and fixes are covered.  

## Background

Gluu Gateway is an API Gateway which leverages the [Gluu Server](https://gluu.org) for central client management and access control using OAuth and UMA scopes. 

## Documentation

Please visit the [Gluu Gateway docs](./index.md) for more complete documentation.   
 
## Gluu Gateway 4.2.1

The most important update in Gluu Gateway 4.2.1 is the upgrade from Kong 2.1.1 to 2.0.4 Additionally, several improvements have been made to metrics and access tokens.

- [#33](https://github.com/GluuFederation/gluu-gateway-ui/issues/33) - Bump googleapis from 33.0.0 to 59.0.0 
- [#29](https://github.com/GluuFederation/gluu-gateway-ui/issues/29) - Upgrade Kong to version 2.1.1
- [#15](https://github.com/GluuFederation/gluu-gateway-setup/issues/15) - Adding installation support for gg in new linux distros.

## Gluu Gateway 4.2

The most important update in Gluu Gateway 4.2 is the upgrade from Kong 2.0.1 to 2.0.4 Additionally, several improvements have been made to metrics and access tokens.

### Changes

- [#407](https://github.com/GluuFederation/gluu-gateway/issues/407) - OpenId Connect user sessions with server side storage
- [#414](https://github.com/GluuFederation/gluu-gateway/issues/414) - Add spontaneous scopes support to gluu-oauth-pep
- [#423](https://github.com/GluuFederation/gluu-gateway/issues/423) - /?? reg expression should not have to add / at last
- [#424](https://github.com/GluuFederation/gluu-gateway/issues/424) - Sometime Getting 400 from kong
- [#426](https://github.com/GluuFederation/gluu-gateway/issues/426) - Ensure that `??` pattern in protected path present not more then one time
- [#432](https://github.com/GluuFederation/gluu-gateway/issues/432) - Fixed Unexpected error and redirection problem
- [#441](https://github.com/GluuFederation/gluu-gateway/issues/441) - Handle POST request after OpenID Connect authentication and redirects

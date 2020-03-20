## Notice

This document, also known as the Gluu Gateway Release Note, relates to the Gluu Gateway Release versioned 4.1.x. The work is licensed under the “[The Apache 2.0](https://www.apache.org/licenses/LICENSE-2.0) License” allowing the use, copy, modify, merge, publish, distribute, sub-license and sale without limitation and liability, provided the end-user person or organization using this software has an active support subscription for this software with either Gluu or one of Gluu's OEM partners after using the software for more than 30 days. This document extends only to the aforementioned release version in the heading.  

UNLESS IT HAS BEEN EXPRESSLY AGREED UPON BY ANY WRITTEN AGREEMENT BEFOREHAND, THE WORK/RELEASE IS PROVIDED “AS IS”, WITHOUT ANY WARRANTY OR GUARANTEE OF ANY KIND EXPRESS OR IMPLIED. UNDER NO CIRCUMSTANCE, THE AUTHOR, OR GLUU SHALL BE LIABLE FOR ANY CLAIMS OR DAMAGES CAUSED DIRECTLY OR INDIRECTLY TO ANY PROPERTY OR LIFE WHILE INSTALLING OR USING THE RELEASE.  

## Purpose

The document is released with Version 4.1.x of the Gluu Gateway Software. The purpose of this document is list changes made and new features included in this release. The list is not exhaustive and there might be some omission of negligible issues, but the noteworthy features, enhancements and fixes are covered.  

## Background

Gluu Gateway is an API Gateway which leverages the [Gluu Server](https://gluu.org) for central client management and access control using OAuth and UMA scopes. 

## Documentation

Please visit the [Gluu Gateway docs](./index.md) for more complete documentation.   
 
## Gluu Gateway 4.1

The most important update in Gluu Gateway 4.1 is the upgrade from Kong 1.3 to 2.0. Additionally, several improvements have been made to metrics and access tokens.

### Changes
- [#420](https://github.com/GluuFederation/gluu-gateway/issues/420) Kong 2.0
- [#418](https://github.com/GluuFederation/gluu-gateway/issues/418) resty/hmac.lua throwing error in kong 1.5
- [#416](https://github.com/GluuFederation/gluu-gateway/issues/416) Bump Kong to 1.5.0
- [#417](https://github.com/GluuFederation/gluu-gateway/issues/417) bump Kong to 1.5.0
- [#342](https://github.com/GluuFederation/gluu-gateway/issues/342) Update Grafana configuration and make reports
- [#411](https://github.com/GluuFederation/gluu-gateway/issues/411) One bug in gluu-metrics plugin
- [#370](https://github.com/GluuFederation/gluu-gateway/issues/370) OpenID plugin configuration for UMA access control: Push UserInfo token
- [#402](https://github.com/GluuFederation/gluu-gateway/issues/402) Update submodule oxd-web-lua
- [#403](https://github.com/GluuFederation/gluu-gateway/issues/403) update oxdweb to latest master
- [#400](https://github.com/GluuFederation/gluu-gateway/issues/400) inactive oxd access token
- [#399](https://github.com/GluuFederation/gluu-gateway/issues/399) add ability to run test suite against any docker image
- [#396](https://github.com/GluuFederation/gluu-gateway/issues/396) write a script to run our test suite against an image from Dockerhub
- [#382](https://github.com/GluuFederation/gluu-gateway/issues/382) Map access_token to HTTP header in OIDC plugin, Default: off
- [#273](https://github.com/GluuFederation/gluu-gateway/issues/273) Add unsupported JWT signing algorithms
- [#394](https://github.com/GluuFederation/gluu-gateway/issues/394) lua folder structure, custom headers, OpenId Connect access tokens
- [#390](https://github.com/GluuFederation/gluu-gateway/issues/390) Maintain access_token in user session, gluu-openid-connect
- [#392](https://github.com/GluuFederation/gluu-gateway/issues/392) Refactor Lua code folder structure
- [#381](https://github.com/GluuFederation/gluu-gateway/issues/381) Add Custom header map configuration for OAuth and UMA plugins as well
- [#393](https://github.com/GluuFederation/gluu-gateway/issues/393) Farther GG repo decomposition
- [#377](https://github.com/GluuFederation/gluu-gateway/issues/377) Refactor test suite to use GG docker image
- [#373](https://github.com/GluuFederation/gluu-gateway/issues/373) Map Userinfo and id_token claims to HTTPS headers
- [#379](https://github.com/GluuFederation/gluu-gateway/issues/379) Make Oauth2 client Kong consumer mapping optional
- [#365](https://github.com/GluuFederation/gluu-gateway/issues/365) GG Lua deps and plugins archive
- [#376](https://github.com/GluuFederation/gluu-gateway/issues/376) Update setup script to remove daos.lua for safe kong db migration



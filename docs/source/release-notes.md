## Notice

This document, also known as the Gluu Gateway Release Note, relates to the Gluu Gateway Release versioned 4.0.x. The work is licensed under the “[Gluu Support License](https://raw.githubusercontent.com/GluuFederation/gluu-gateway/master/LICENSE)” allowing the use, copy, modify, merge, publish, distribute, sub-license and sale without limitation and liability, provided the end-user person or organization using this software has an active support subscription for this software with either Gluu or one of Gluu's OEM partners after using the software for more than 30 days. This document extends only to the aforementioned release version in the heading.  

UNLESS IT HAS BEEN EXPRESSLY AGREED UPON BY ANY WRITTEN AGREEMENT BEFOREHAND, THE WORK/RELEASE IS PROVIDED “AS IS”, WITHOUT ANY WARRANTY OR GUARANTEE OF ANY KIND EXPRESS OR IMPLIED. UNDER NO CIRCUMSTANCE, THE AUTHOR, OR GLUU SHALL BE LIABLE FOR ANY CLAIMS OR DAMAGES CAUSED DIRECTLY OR INDIRECTLY TO ANY PROPERTY OR LIFE WHILE INSTALLING OR USING THE RELEASE.  

## Purpose

The document is released with Version 4.0.x of the Gluu Gateway Software. The purpose of this document is list changes made and new features included in this release. The list is not exhaustive and there might be some omission of negligible issues, but the noteworthy features, enhancements and fixes are covered.  

## Background

Gluu Gateway is an API Gateway whichh leverages the [Gluu Server](https://gluu.org) for central client management and access control using OAuth and UMA scopes. 

## Documentation

Please visit the [Gluu Gateway docs](./index.md) for more complete documentation.   
 
## Gluu Gateway 4.0

There are major changes in GG 4.0. Plugins have been de-coupled and separated in to two categories: authentication and authorization. In addition, two new plugins have been added: `gluu-openid-connect` and `gluu-opa-pep`.  

A description of our plugins follows: 

| Plugin | Description | 
|--------|-------------|
|**gluu-oauth-auth**| Authenticate client by OAuth Token|
|**gluu-uma-auth**| Authenticate client by RPT|
|**gluu-openid-connect**| Authenticate client by OIDC code flow|
|**gluu-oauth-pep**| Authorization by OAuth token scopes|
|**gluu-opa-pep**| Authorization plugin for [Open Policy Agent](https://www.openpolicyagent.org/)|
|**gluu-uma-pep**| Authorization by UMA Scope security|
|**gluu-metics**| Metrics about client authentication, authorization and others|

### Changes
- [#297](https://github.com/GluuFederation/gluu-gateway/issues/297): Decouple existing gluu-uma-pep. Now there are two separate plugins 1. gluu-uma-auth 2. gluu-uma-pep.
- [#298](https://github.com/GluuFederation/gluu-gateway/issues/298): Decouple existing gluu-oauth-pep. Now there are two separate plugins 1. gluu-oauth-auth 2. gluu-oauth-pep.
- [#328](https://github.com/GluuFederation/gluu-gateway/issues/328) : Collect more metrics about all plugins
- [#336](https://github.com/GluuFederation/gluu-gateway/issues/336) : GG UI: Remove extra API for plugin config

### New Features
- [#283](https://github.com/GluuFederation/gluu-gateway/issues/283): New `gluu-openid-connect` plugin with UMA claim gathering support 
- [#296](https://github.com/GluuFederation/gluu-gateway/issues/296): Admin GUI for plugins configuration
- [#320](https://github.com/GluuFederation/gluu-gateway/issues/320) : Make new Open Policy Agent plugin `gluu-opa-pep`
- [#317](https://github.com/GluuFederation/gluu-gateway/issues/317) : Support Phantom Token Flow

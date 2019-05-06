## Notice

This document, also known as the Gluu Gateway Release Note, relates to the Gluu Gateway Release versioned 2.0.x. The work is licensed under “[The Gluu Support License](https://raw.githubusercontent.com/GluuFederation/gluu-gateway/master/LICENSE)” allowing the use, copy, modify, merge, publish, distribute, sub-license and sale without limitation and liability, provided the end-user person or organization using this software has an active support subscription for this software with either Gluu or one of Gluu's OEM partners after using the software for more than 30 days. This document extends only to the aforementioned release version in the heading.  

UNLESS IT HAS BEEN EXPRESSLY AGREED UPON BY ANY WRITTEN AGREEMENT BEFOREHAND, THE WORK/RELEASE IS PROVIDED “AS IS”, WITHOUT ANY WARRANTY OR GUARANTEE OF ANY KIND EXPRESS OR IMPLIED. UNDER NO CIRCUMSTANCE, THE AUTHOR, OR GLUU SHALL BE LIABLE FOR ANY CLAIMS OR DAMAGES CAUSED DIRECTLY OR INDIRECTLY TO ANY PROPERTY OR LIFE WHILE INSTALLING OR USING THE RELEASE.  

## Purpose

The document is released with the Version 2.0.x of the Gluu Gateway Software. The purpose of this document is to provide the changes made/new features included in this release of the Gluu Software. The list is not exhaustive and there might be some omission of negligible issues, but the noteworthy features, enhancements and fixes are covered.  

## Background

The Gluu Server is an API Gateway leveraging the Gluu Server for central client management and access control using OAuth and UMA scopes. The Gluu Server is a container distribution composed of software written by Gluu and incorporated from other open source projects.  

## Documentation

Please visit the [Gluu Gateway Documentation Page](http://www.gluu.org/docs/gg) for the complete 
documentation and administrative guide.   
 
## Gluu Gateway 2.0

There are major changes in GG 2.0. We de-couple plugins and separate it in two categories, authentication and authorization. Also we provide one new plugin that is `gluu-openid-connect` for OpenID Connect authorization code flow. Now there is 5 plugins.

| Plugin | Description | Priority |
|--------|-------------|----------|
|**gluu-oauth-auth**| Authenticate client by OAuth Token|999|
|**gluu-oauth-pep**| Authorization by OAuth token scopes|996|
|**gluu-uma-auth**| Authenticate client by RPT|998|
|**gluu-uma-pep**| Authorization by UMA Scope security|995|
|**gluu-openid-connect**| Authenticate client by code flow|997|
|**gluu-metics**| Metrics about client authentication, authorization and others|14|

!!! Note
    The higher the priority, the sooner your plugin’s phases will be executed in regard to other plugins’ phases 

### Changes
- [#297](https://github.com/GluuFederation/gluu-gateway/issues/297): Decouple existing gluu-uma-pep. Now there are two separate plugins 1. gluu-uma-auth 2. gluu-uma-pep.
- [#298](https://github.com/GluuFederation/gluu-gateway/issues/298): Decouple existing gluu-oauth-pep. Now there are two separate plugins 1. gluu-oauth-auth 2. gluu-oauth-pep.

### Features
- [#283](https://github.com/GluuFederation/gluu-gateway/issues/283): New `gluu-openid-connect` plugin with UMA claim gathering support 
- [#296](https://github.com/GluuFederation/gluu-gateway/issues/296): Admin GUI for plugins configuration

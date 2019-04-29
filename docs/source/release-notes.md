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
 
## Gluu Gateway 2.0.1

Includes minor fixes in the oxd Web Application, a subcomponent of Gluu Gateway (GG).  

### Fixes 
- [oxd #292](https://github.com/GluuFederation/oxd/issues/292) Fixed oxd `key_from_script` issue and added support for the introspection custom interception script.  
- [#303](https://github.com/GluuFederation/gluu-gateway/issues/303) Updated GG Admin GUI login to work with changes to oxd userinfo response.  

### Changes
- [#311](https://github.com/GluuFederation/gluu-gateway/issues/311) The GG setup script now installs and configures oxd.  
- oxd now comes with GG as a package rather than a dependency. After installation, the oxd package is located in the `/tmp` directory. 

## Gluu Gateway 2.0 

API Gateway leveraging the Gluu Server for central client management and access control using OAuth and UMA scopes.  

Gluu Gateway includes three plugins:  
1. Gluu-OAuth-PEP  
1. Gluu-UMA-PEP  
1. Gluu-Metrics  
 
It also comes with the [Kong](https://konghq.com/) proxy and the [oxd Web Application](https://gluu.org/docs/oxd) as dependencies.  

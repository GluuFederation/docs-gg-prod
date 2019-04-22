## Gluu Gateway 1.0.1

Minor fixes in the oxd Web Application, a subcomponent of Gluu Gateway (GG).

### Fixes 
- [oxd #292](https://github.com/GluuFederation/oxd/issues/292) Fixed oxd `key_from_script` issue and added support for the introspection custom interception script.
- [#303](https://github.com/GluuFederation/gluu-gateway/issues/303) Updated GG Admin GUI login to work with changes to oxd userinfo response.

### Changes
- [#311](https://github.com/GluuFederation/gluu-gateway/issues/311) The GG setup script now installs and configures oxd.
- oxd now comes with GG as a package rather than a dependency. After installation, the oxd package is located in the `/tmp` directory. 

## Gluu Gateway 1.0 

API Gateway leveraging the Gluu Server for central client management and access control using OAuth and UMA scopes.

Gluu Gateway includes three plugins:
1. Gluu-OAuth-PEP
1. Gluu-UMA-PEP
1. Gluu-Metrics

It also comes with the [Kong](https://konghq.com/) proxy and the [oxd Web Application](https://gluu.org/docs/oxd) as dependencies.

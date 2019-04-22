## Gluu Gateway 1.0.1

Here is small fixes in GG sub component i.e. OXD Web Application, all other functionality is same as GG 1.0.

### Fixes 
- Fixed problem in OXD about `key_from_script` and added introspection interceptions Custom script supports.
- Updated GG Admin GUI login as OXD userinfo response it updated.

### Changes
- OXD comes with GG as package not as dependency. After installation you can get OXD package from `/tmp` directory. 
- OXD install and configure using GG setup script.

## Gluu Gateway 1.0 

This is first Gluu Gateway release with Gluu 3 plugins and Admin GUI for Plugins configuration.

There are 3 plugins:
1. Gluu-OAuth-PEP
1. Gluu-UMA-PEP
1. Gluu-Metrics

It comes with [Kong](https://konghq.com/) proxy and OXD Web Application(OpenID Middleware server) as dependencies.

Please check [docs](https://gluu.org/docs/gg) for all other features and security facilities.   

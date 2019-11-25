# Plugin Introduction

## Overview

## Plugin Preference

Plugins can be configured to run on the service, route, consumer, or global level. The plugin will only run once, even if configured on multiple levels. This allows a plugin to behave one way for most requests, but differently for a specific type of request. Gluu Gateway has adopted Kong's order of plugin precedence, as follows:

1. Plugins configured on a Route, a Service, **and** a Consumer.
1. Plugins configured on a Route **and** a Consumer.
1. Plugins configured on a Service **and** a Consumer.
1. Plugins configured on a Route **and** a Service.
1. Plugins configured on a Consumer.
1. Plugins configured on a Route.
1. Plugins configured on a Service.
1. Plugins configured to run Globally.

!!! Note
    Plugins configured on the Consumer level requires requests to be authenticated. 
    
## Add a plugin

### Add a plugin on the service level

1. Click `SERVICES` on the left panel
1. Click on `name` or `edit` button
1. Click on `Plugins`
1. Click on `+ ADD PLUGIN` button
1. Select the desired plugin and click its `+` icon
1. Fill in the [plugin's parameters](#available-plugins)

### Add a plugin on the route level 

1. Click [`ROUTES`](../../admin-gui/#routes) on the left panel
1. Click on `route id/name` or `edit` button
1. Click on [`Plugins`](../../admin-gui/#route-plugins)
1. Click on `+ ADD PLUGIN` button
1. Select the desired plugin and click its `+` icon
1. Fill in the [plugin's parameters](#available-plugins)

### Add a plugin globally

A global plugin will apply to all services and routes.

1. Click [`Plugins`](../../admin-gui/#plugins) on the left panel
1. Select the desired plugin and click its `+` icon
1. Fill in the [plugin's parameters](#available-plugins)

## Available plugins

<!-- Add links to appropriate pages -->

### Authentication
- gluu-oauth-auth
- gluu-uma-auth
- gluu-openid-connect

### Policy Enforcement Point
- gluu-oauth-pep
- gluu-uma-pep
- gluu-opa-pep

### General
- Metrics

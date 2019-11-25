# Plugin Introduction

## Overview

## Plugin Preference

Plugins can be configured to run on the service, route, or global level. If configured on multiple levels, the plugin will only run once, based on plugin preference. This allows a plugin to behave one way for most requests, but differently for a specific type of request. Gluu Gateway has adopted Kong's order of plugin precedence, as follows:

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
    
## Configuration


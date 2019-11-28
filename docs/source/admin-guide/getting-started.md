# Getting started with GG Admin GUI

This guide will describe you to use Gluu Gateway Admin UI to configure the upstream application with Kong, added plugin and security configuration.

## Dashboard - Kong Proxy Details

The first section on the top of the navigation is the dashboard. This section shows application credentials and configuration details. Let's take a look by sections wise.

1. **Global Info:** It shows the OXD OP Client credentials which is used to login into GG UI. GG UI is just a UI to configure the Kong and plugins. which we protected using OpenID Connect flow. It shows you the `OXD Id`, `Client Id` and `Client Secret` used by GG UI. This credentials has been created during installation step.

2. **Gateway:** It show details about the Gateway itself. This is the details which you need to use to hit kong proxy endpoint. After GG installation it exposed some ports and endpoints as below
     - **Proxy endpoint:** It exposed on `443` port. This is the endpoint which you globally expose and will hit by the end-users or client applications. 
     - **Admin API endpoint:** It is important endpoint expose by the kong. which is locally available on `8001` http and `8445` https. It is used to configure service, routes, consumers and plugins.  

3. **Database Info:** It shows you details about the Database which is used by Kong.

4. **Plugins:** It displays all the plugins supported by the Gluu Gateway. When inactive, a plugin is shown as gray. When plugin is added to an API/Consumer or globally, its name will turn green on the dashboard.

5. The remaining subsections, **Requests**, **Connections** and **Timers** show real-time metrics for Gluu Gateway's health.  

[![dashboard](../img/1_dashboard.png)](../img/1_dashboard.png)

## Info - Kong configuration details

The Info section shows generic details about the Kong node. In short it shows all the details which is provided by `Kong Admin API Endpoint` that is `http://localhost:8001`. It shows every setting which you have configured in `/etc/kong/kong.conf` file. Use this file to update kong proxy port, admin api port, ssl certificates and other kong configurations. 

[![info](../img/2_info.png)](../img/2_info.png)


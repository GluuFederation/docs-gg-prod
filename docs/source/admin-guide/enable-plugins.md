# Plugins

A plugin entity represents a plugin configuration that will be executed during the HTTP request/response lifecycle. Plugins add functionality to services that run behind Kong, such as Authentication, Security, Rate Limiting and others.

Plugins can be added on the [Service](#add-plugins-on-service), [Route](#add-plugins-on-route), or [Global](#add-plugins-globally) levels.

## Add Plugins on Service

If you add plugins in a Service entity, the plugin will execute for every route under the service.

For example: One service `test-service1` is created with two routes, `route1` and `route2`. One plugin is added on `test-service1`. When the user requests `route1`, the plugin will be executed. The same is true if the user requests `route2`. Overall, both requests target the same service with the attached plugin.

Below are the steps to enable plugins on the Service entity.

- Go to `Services` view by clicking on `Services` in left side of the navigation bar.

- Click on the Service name or pencil icon on which you want to add plugins.

- Click on the `Plugins` tab.

- Click on the `+ ADD PLUGIN` button.

- Now click on `+` icon of the plugin which you want to add

[![6_plugins](../img/add-plugins-on-service.png)](../img/add-plugins-on-service.png)

## Add Plugins on Route

If you add plugins to a Route entity, the plugin will only execute for that route.

For example: One service `test-service1` is created with two routes, `route1` and `route2`. One plugin is added on `route1`. When a user requests `route1`, it will execute the plugin, but **not** if the user requests `route2`.

Below are the steps to enable plugins on the Route entity.

- Go to `Routes` view by clicking on `Routes` in left side of the navigation bar.

- Click on the Route name or pencil icon on which you want to add plugins.

- Click on the `Plugins` tab.

- Click on the `+ ADD PLUGIN` button.

- Now click on `+` icon of the plugin which you want to add.

[![6_plugins](../img/add-plugins-on-route.png)](../img/add-plugins-on-route.png)

## Add Plugins Globally

If a plugin is added globally, it will apply for all services and routes.

- Go to `Plugins` view by clicking on `Plugins` in left side of the navigation bar.

- Add Plugins by using the `+ ADD GLOBAL PLUGINS` button.

- Now click on `+` icon of the plugin which you want to add.

[![6_plugins_add](../img/6_plugins_add.png)](../img/6_plugins_add.png)

## Gluu Plugin Combinations and scopes

The following table describes the possible combination of Gluu Plugins for security, as well as where the plugins can be added.

| Plugin | Service | Route | Globally |
|--------|---------|-------|----------|
|gluu-oauth-auth| ✔ | ✔ | ✔ |
|gluu-oauth-auth and gluu-oauth-pep| ✔ | ✔ | ✔ |
|gluu-oauth-auth and gluu-opa-pep| ✔ | ✔ | ✔ |
|gluu-uma-auth and gluu-uma-pep| ✔ | ✔ | ✔ |
|gluu-openid-connect|-|✔|-|
|gluu-openid-connect and gluu-uma-pep|-|✔|-|
|gluu-openid-connect and gluu-opa-pep|-|✔|-|
|gluu-metrics|-|-|✔|

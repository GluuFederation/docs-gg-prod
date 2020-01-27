# OpenID Connect Authentication and UMA PEP Authorization 

## Overview

In this tutorial, we are going to add `OpenID Connect Authorization code flow` authentication by configuring the [`gluu-openid-connect`](../../plugin/gluu-openid-connect-uma-pep/) plugin and user authorization by the [`gluu-uma-pep`](../../plugin/gluu-uma-pep/) plugin. User will be authenticate first and second part is to check the user permission using the UMA Policy. 

In the demo, the user will first be **authenticated by OpenID Connect** and for `/settings` resource, user will be prompted to enter their `country` and `city` before being able to access a protected page. If the values entered are correct (US, NY), the user is granted access. If not, access is denied. We are using default UMA Policy available in Gluu CE which check the `Country=US` and `City=NY`. You can modify this policy and write the custom login to authorized user.    

!!! Info
    If you have 2-3 policies then it will be good to use UMA. But If you have much more numbers of policies then [OPA](../oidc-opa-tutorial) is the best option.

## Requirements

- Gluu Gateway 4.1: This is our an OpenID Connect relying party(RP) between the client and the upstream service. [Install Gluu Gateway](../installation.md). [OXD Server](https://gluu.org/docs/oxd/4.0/) is a static APIs web application which will install during GG installation.

- Gluu Server 4.0: This is our OpenID Connect Server. [Install Gluu](https://gluu.org/docs/ce/4.0/installation-guide/install-ubuntu/)

- Protected(Upstream) Website: In our demo, we are using a demo Node.js App. Take Node.js demo from [here](https://github.com/GluuFederation/gluu-gateway-setup/tree/version_4.1/gg-demo/node-ejs). 

## Gluu Gateway configuration (RP)

!!! Note
    The GG UI is only available on the localhost. Since it is on a remote machine, we need SSH port forwarding to reach the GG UI. Plugin configuration can be done either via REST calls or via the Gluu Gateway web interface.  

Applications and their ports:

| Port | Description |
|------|-------------|
|1338| Gluu Gateway Admin GUI|
|8001|Kong Admin API|
|8000|Kong Proxy Endpoint|
|443|Kong SSL Proxy Endpoint. Kong by default provide 8443 port for SSL proxy but during setup, it changes into 443.|
|8443|oxd Server| 

Login into Gluu Gateway Admin GUI(:1338) and follow the below steps.

### Add Service

Register your upstream website as a Service.

We are using [`http://localhost:4400`](https://github.com/GluuFederation/gluu-gateway-setup/tree/version_4.1/gg-demo/node-ejs) as the Upstream Website, it is your application where you want to add OpenID Connect Authentication. End-users always request to first kong proxy, the plugin will perform authentication and if all is ok then kong will forward a request to the upstream website and serve content which is return by the upstream website.

Follow these step to add Service using GG UI
 
- Click `SERVICES` on the left panel
- Click on `+ ADD NEW SERVICE` button
- Fill in the following boxes:
    - **Name:** oidc-opa-demo
    - **URL:** http://localhost:4400

![oidc-uma-1.png](../img/oidc-uma-1.png)

### Add Route

Follow these steps to add a route:

- Click on the `oidc-uma-demo` service

- Click `Routes`

- Click the `+ ADD ROUTE` button

- Fill in the following boxes:
     - **Name:** oidc-uma-demo
     - **Hosts:** `<your-server-host>`, `Tip: Press Enter to accept value`. In my case, I am using server and updated `/etc/hosts` file. This is the host which we will use to request in a browser after configuration. You can register your domain host if you are using live servers. For further next tutorial, I am using `dev1.gluu.org`, you need to use your host. Check kong docs for more routing capabilities [here](https://docs.konghq.com/1.0.x/proxy/#routes-and-matching-capabilities).
  
![oidc-uma-2.png](../img/oidc-uma-2.png)

### Configure `gluu-openid-connect` plugin

- Click `ROUTES` on the left panel
- Click on `route id/name` which has host `dev1.gluu.org`
- Click on `Plugins`
- Click on `+ ADD PLUGIN` button
- You will see `Gluu OIDC & UMA PEP` title and `+` icon in pop-up.
- Click on the `+` icon and it will show below form.
    - Enable `UMA PEP Security configuration` and disabled `ACR Expression`.
    - Fill `UMA PEP Security configuration` section form with the below details

|Fields|Value|Description|
|-----|-------|----------|
|Path|/settings/?|it will protect path `/settings/` and also any sub path in settings path.|
|HTTPMethods|?|it will used to protect the HTTP Methods. `?` means all the HTTP methods. For Example: `GET`, `POST`, all others.|
|scope|with-claims|it is just a name of the scope. GG UI will create UMA scope in your Gluu CE. In Gluu CE UI(oxtrust) you need to add the UMA Policy in this scope|  
|Deny By Default|No(false)|it is optional. `false` means it is will allow unprotected path i.e. the path which is not registered. We registered only `/settings/??` path so except this path all other path are unprotected path.|

![oidc-uma-1.png](../img/oidc-uma-3.png)
![oidc-uma-1.png](../img/oidc-uma-4.png)
![oidc-uma-1.png](../img/oidc-uma-5.png)
![oidc-uma-1.png](../img/oidc-uma-6.png)

This completes the configuration. Next, request the Kong proxy at `https://<your_host>/settings/` in the browser. As per my configuration, I am requesting `https://dev1.gluu.org/settings/`.

!!! Important
    The request should be `/settings/` not a `/settings`. Look [here](../../plugin/common-features/#dynamic-resource-protection) for more detail about dynamic path registration.

## Authentication

1. Once you request to kong proxy, the plugin will redirect you to your OP side.

     ![oidc-demo10](../img/oidc-demo10.png)
     
     After successful authentication, OP will show you all requested permissions, click on `Allow`.
     
     ![oidc-demo10](../img/oidc-demo11.png)

2. After `allow`, you will get back to kong proxy and plugin will serve you `/flights` page.

     ![oidc-demo7](../img/opa-demo7.png)
     
3. Now try to click on any other page. let's click on `payments`. It will deny you because the policy only allows `flights`.

     ![oidc-demo8](../img/opa-demo8.png)

You can check the kong's [`error.log`](../../logs) file or [`gluu-opa-pep docs`](../../plugin/gluu-opa-pep) to check the request to OPA server and which data are passed to OPA endpoint.  

For more details and configuration check [`gluu-openid-connect` plugin docs](../../plugin/gluu-openid-connect-uma-pep/) and for [`gluu-opa-pep` check docs](../../plugin/gluu-opa-pep/).

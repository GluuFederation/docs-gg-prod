# OpenID Connect Stepped-up Authentication 

## Overview

This tutorial describes how to add `OpenID Connect Authorization code flow` stepped-up authentication by configuring the [`gluu-openid-connect`](../../plugin/gluu-openid-connect-uma-pep/) plugin. 

In the demo, the user will first authenticate with basic (`auth_ldap_server`) auth, then needs to pass one more `OTP` authentication step for the `/payments/` resource. 

It is fully configurable, you can add stepped-up authentication on multiple resources. Check [here](../../plugin/gluu-openid-connect-uma-pep/) for more details. 

## Requirements

- Gluu Gateway 4.0: This is an OpenID Connect relying party(RP) between the client and the upstream service. [Install Gluu Gateway](../installation.md). The [oxd server](https://gluu.org/docs/oxd/4.0/) is a static API web application that will install during GG installation.

- [Gluu Server 4.0](https://gluu.org/docs/ce/4.0/installation-guide/install-ubuntu/)

- Protected (Upstream) Website: In our demo, we are using a demo Node.js App, available [here](https://github.com/GluuFederation/gluu-gateway/tree/version_4.0/gg-demo/node-ejs). 

## Gluu Server configuration (OpenID Connect Server)
   
First, add `OTP` stepped-up authentication by enabling the `OTP` ACR in the OP Server. Configure the following settings inside your Gluu Server: 

1. In oxTrust, navigate to `Configuration` > `Manage Custom Scripts` 

1. Enable the `OTP` script
     
     ![oidc-demo9](../img/oidc-demo9.png)

1. Now just confirm that it is enabled successfully by checking your OP discovery endpoint `<your_op_server>/.well-known/openid-configuration`, it should show `otp` in the `acr_values_supported` property.

## Gluu Gateway configuration (RP)

In this demo, we are going to register and protect the whole upstream service (the website) using `gluu-openid-connect` plugin. We will register the `/payments/??` path with the `OTP` ACR and for all other resources, the default `auth_ldap_server`. As a result, a request for `/payments` will ask for an additional `OTP` authentication step to access the resource.     

!!! Note
    The GG UI is only available on the localhost. Since it is on a remote machine, we need SSH port forwarding to reach the GG UI. Plugin configuration can be done either via REST calls or via the Gluu Gateway web interface.  

Applications and their ports:

| Port | Description |
|------|-------------|
|1338| Gluu Gateway Admin GUI|
|8001|Kong Admin API|
|8000|Kong Proxy Endpoint|
|443|Kong SSL Proxy Endpoint. Kong by default provides port 8443 for SSL proxy, but during setup changes it to 443.|
|8443|oxd Server| 

Log in to the Gluu Gateway Admin GUI(:1338) and follow the below steps.

### Add Service

Register the upstream website as a Service.

This demo uses [`http://localhost:4400`](https://github.com/GluuFederation/gluu-gateway/tree/version_4.0/gg-demo/node-ejs) as the Upstream Website, the application where OpenID Connect Authentication is added. End-users always request to the Kong proxy first, then the plugin performs authentication. If it's successful, Kong will forward the request to the upstream website and serve the content returned by the upstream website.

Follow these step to add a Service using GG UI
 
- Click `SERVICES` on the left panel
- Click on `+ ADD NEW SERVICE` button
- Fill in the following boxes:
    - **Name:** oidc-steppedup-demo
    - **URL:** http://localhost:4400

![oidc-demo1](../img/oidc-demo1.png)

### Add Route

Follow these steps to add a route:

- Click on the `oidc-steppedup-demo` service

- Click `Routes`

- Click the `+ ADD ROUTE` button

- Fill in the following boxes:
     - **Name:** oidc-steppedup-demo
     - **Hosts:** `<your-server-host>`, `Tip: Press Enter to accept value`. This tutorial uses a server with an updated `/etc/hosts` file. This is the host that will be requested in the browser after configuration. If using live servers, register the domain host instead. The rest of the tutorial will use `dev1.gluu.org` as an example, replace it with your host. Check the [Kong docs](https://docs.konghq.com/0.14.x/proxy/#routes-and-matching-capabilities) for more routing capabilities.
  
![oidc-demo2](../img/oidc-demo2.png)

### Configure Plugin

- Click `ROUTES` on the left panel
- Click on the `route id/name` with `dev1.gluu.org` as the host
- Click on `Plugins`
- Click on `+ ADD PLUGIN` button
- You will see `Gluu OIDC & UMA PEP` title and `+` icon in pop-up.
- Click on the `+` icon and it will show the below form. Add the ACR expression as in the below screenshots.
    - `OTP` stepped-up auth for path `/payments/??`
    - `auth_ldap_server` authentication for all other paths. Check [here](../../plugin/gluu-openid-connect-uma-pep/#dynamic-url-base-acrs-stepped-up-authentication) for more details about ACR expressions.

![oidc-demo3](../img/oidc-demo3.png)

![oidc-demo4](../img/oidc-demo4.png)

![oidc-demo5](../img/oidc-demo5.png)

![oidc-demo6](../img/oidc-demo6.png)

![oidc-demo7](../img/oidc-demo7.png)

![oidc-demo8](../img/oidc-demo8.png)

This completes configuration. Next, request the Kong proxy at `https://<your_host>` in the browser. In this example, the host is `https://dev1.gluu.org`.

## Authentication

1. Once you send a request to the Kong proxy, the plugin will redirect the request to the OP side. The OP will request for the `username` and `password`, because we added the `auth_ldap_server` ACR for any path `/??`.

     ![oidc-demo10](../img/oidc-demo10.png)
     
     After successful authentication, the OP will display all requested permissions. Click `Allow`.
     
     ![oidc-demo11](../img/oidc-demo11.png)

2. After clicking `allow`, you will get back to the Kong proxy and the plugin will serve the default home page of the upstream service.

     ![oidc-demo12](../img/oidc-demo12.png)
     
     Click on `Flights`. It is also in the `/??` path, so the user already has permission to access this resource.
     
     ![oidc-demo1](../img/oidc-demo13.png)

3. Now click `Payments`, on which we added the `OTP` stepped-up authentication. The plugin will redirect again to the OP. As per the `OTP` script, it will ask for the `username` and `password`.

     ![oidc-demo10](../img/oidc-demo10.png)
     
     After successful authentication, the OP Server asks you to enroll in a device. Scan the displayed QR Code in an authenticator application, then click on `Finish`. Check the [Gluu CE docs](https://gluu.org/docs/ce/authn-guide/otp/#recommended-otp-apps) for supported OTP applications.
     
     ![oidc-demo10](../img/oidc-demo15.png)
     
     After successful enrollment, it will prompt to enter the OTP. Enter the OTP from the authenticator application and click on `Login`.
     
     ![oidc-demo10](../img/oidc-demo14.png)

4. After `OTP` authentication, the OP server will redirect back to the Kong proxy and serve the `Payments` page. 

     ![oidc-demo10](../img/oidc-demo16.png)

For more details and configuration, check the `gluu-openid-connect` [plugin docs](../../plugin/gluu-openid-connect-uma-pep/).

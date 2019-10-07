# Frequently Asked Questions (FAQ)

## General FAQs and troubleshooting

### Where should Gluu Gateway be used?
Gluu Gateway should be used in every API gateway where you need advanced security measures using OAuth 2.0 and User-Managed Access (UMA) 2.0 Grant. Easy to add OpenID Connect Authentication.

### How to get more idea about Services and Routes configuration in Kong?
Please check the [Kong 1.3.x docs about proxy configuration](https://docs.konghq.com/1.3.x/proxy/).

### How can I investigate Gluu Gateway problems?
Gluu Gateway uses Gluu Server and oxd. That means any potential issue on these servers can influence the work of Gluu Gateway.

![](./img/10_oxd_error_faq.png)

If you see an error message such as the one in the screenshot, check the logs for possible issues:

`/var/log/oxd-server/oxd-server.log`

You can also check the [oxd FAQ section](https://gluu.org/docs/oxd/faq).

### How can I find my Gluu Gateway version?
Your Gluu Gateway version is always visible in the bottom left corner of the Gluu Gateway Admin Panel. 

## Technical FAQs

### How can I generate an OAuth token?
Generate an OAuth token during OAuth-PEP authentication by calling the oxd-server endpoint `/get-client-token` with customer credentials. Read more OAuth generation [here](https://gluu.org/docs/oxd/api/#get-client-token).

### How can I generate UMA tokens?
In order to generate an UMA token, follow these steps:  

1. Send a request to the Kong proxy API without a token and get a ticket.  

1. Send a `get-client-token` request with Consumer `oxd_id`, `client_id` and `client_secret`   

1. Send an `uma-rp-get-rpt` request with Consumer `oxd_id`, the ticket from Step 1 and the access token from Step 2.  

### How can I change the listening address and port?
By default, Gluu Gateway listens to localhost only, but you can change it manually by configuring the `local.js` config file using the [Configuration section](./configuration.md#admin-gui-portal-konga). You just need to update the `explicitHost` to your global IP or remove this attribute. Read more about the configuration [here](https://0.12.sailsjs.com/documentation/reference/configuration/sails-config).
 
 
If you require any further support, please open a ticket on the [Gluu support portal](https://support.gluu.org).

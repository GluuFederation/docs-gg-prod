# Kong Databaseless Setup

## Overview 

Kong has an excellent option to set it up without the Postgres database. You just need to save the configuration in configuration file, in YAML or JSON format, using declarative configuration.

Unfortunately, there is no Kong API for databaseless configuration, so it can't be set up using the GG UI Admin Panel. You need to add all the details by yourself in a declarative configuration file. Also you need to manually create the OP Clients using the oxd `/register-client` endpoint and pass all the credentials to plugin configuration.

When clustering, the configuration in all nodes should be the same.

## Configuration

To use Kong in DB-less mode, set the `database` directive of kong.conf to `off`. As usual, you can do this by editing `kong.conf` and setting `database=off` and `declarative_config=/etc/kong/kong.yml`.

### Creating a Declarative Configuration File

=== "Community edition"
    ## VM based installation

    To load entities into DB-less Kong, we need a declarative configuration file. Run the following command to create the configuration file:

    ```bash
    cd /etc/kong
    touch kong.yml
    ```

    Let's configure the `gluu-oauth-auth` plugin with DB-Less mode.

    1. First, create two OP Clients for the `gluu-oauth-auth` plugin using the oxd `/register-site` endpoint.

        The first is for the `gluu-oauth-auth` plugin, which is responsible for introspecting the token. Below is the request to create a client using the oxd endpoint. It will return the `oxd_id`, `client_id` and `client_secret`. You need to use this configurations in the `gluu-oauth-auth` plugin configuration.

          ```bash
          curl -k -X POST https://<oxd_host>/register-site \
          -H 'Content-Type: application/json' \
          -d '{
                "op_host": "https://<your_op_host>",
                "redirect_uris": [
                  "https://client.example.com/cb"
                ],
                "client_name": "gluu-oauth-client",
                "scope": [
                  "openid",
                  "oxd"
                ],
                "grant_types": [
                  "client_credentials"
                ]
              }'
          ```

          The second client is for the Kong Consumer. It will return `oxd_id`, `client_id` and `client_secret`. 

          ```bash
          curl -k -X POST https://<your_oxd_host>/register-site \
          -H 'Content-Type: application/json' \
          -d '{
                "op_host": "https://<your_op_host>",
                "redirect_uris": [
                  "https://client.example.com/cb"
                ],
                "client_name": "consumer-client",
                "scope": [
                  "openid",
                  "oxd"
                ],
                "grant_types": [
                  "client_credentials"
                ]
              }'
          ```

    1. Add the following JSON Configuration to the `kong.yml` file.

          ```json
          {
            "_format_version": "1.1",
            "consumers": [
              {
                "custom_id": "<consumer_clients_client_id>",
                "username": "<give_any_unique_name>"
              }
            ],
            "plugins": [
              {
                "config": {
                  "client_id": "<above_oauth_op_clients_client_id>",
                  "client_secret": "<above_oauth_op_clients_client_secret>",
                  "oxd_id": "<above_oauth_op_clients_oxd_id>",
                  "custom_headers": [
                    {
                      "format": "string",
                      "header_name": "x-consumer-id",
                      "value_lua_exp": "consumer.id"
                    },
                    {
                      "format": "jwt",
                      "header_name": "x-oauth-introspect_data",
                      "value_lua_exp": "introspect_data"
                    }
                  ],
                  "op_url": "https://<your_op_host>",
                  "oxd_url": "https://<your_oxd_server_host>"
                },
                "name": "gluu-oauth-auth",
                "service": "demo-service"
              }
            ],
            "routes": [
              {
                "hosts": [
                  "backend.com"
                ],
                "name": "demo-route",
                "service": "demo-service"
              }
            ],
            "services": [
              {
                "name": "demo-service",
                "url": "<your_upstream_app_url"
              }
            ]
          }
          ```

          Set `kong.conf` with `declarative_config=/etc/kong/kong.yml`.
          
          **Certificate Object**
          
          A certificate object represents a public certificate, and can be optionally paired with the corresponding private key. In DB-less mode certificates should be added in kong.yml. Check Kong [Certificate Object](https://docs.konghq.com/2.1.x/admin-api/#certificate-object) docs for more details.
          ```json
          {
            "_format_version": "1.1",
            "consumers": [{ ... }],
            "plugins": [{ ... }],
            "routes": [{ ... }],
            "services": [{ ... }],
            "certificates": [
              {
                 "cert": "-----BEGIN CERTIFICATE-----",
                 "key": "-----BEGIN RSA PRIVATE KEY-----",
                 "snis": [
                    {
                       "name": "ssl-example.com"
                    }
                 ]
              }
            ]
          }
          ```
          
          | **Attributes** | **Description** |
          |----------|-------------|
          | **cert** | PEM-encoded public certificate chain of the SSL key pair. |
          | **key** | PEM-encoded private key of the SSL key pair. |
          | **tags** (optional) | An optional set of strings associated with the Certificate for grouping and filtering. |
          | **snis** | An array of zero or more hostnames to associate with this certificate as SNIs. This is a sugar parameter that will, under the hood, create an SNI object and associate it with this certificate for your convenience. To set this attribute this certificate must have a valid private key associated with it. |
          
          
    3. Checking the Declarative Configuration File.

          ```bash
          sudo kong config -c /etc/kong/kong.conf parse /etc/kong/kong.yml
          ```

    4. Start Kong

          ```bash
          kong start
      ```


=== "Cloud Native Edition"
    ## Kubernetes

    To load entities into DB-less Kong, we need a declarative configuration file. Run the following command to create the configuration file:

    ```bash
    touch kong.yml
    ```

    Let's configure the `gluu-oauth-auth` plugin with DB-Less mode.
    
    1. Log into oxAuth pod.
    
           ```bash
           kubectl exec -ti <oxauth-pod-name> -n <gluu-namespace> -- bash
           ```

    1. First, create two OP Clients for the `gluu-oauth-auth` plugin using the oxd `/register-site` endpoint.

        The first is for the `gluu-oauth-auth` plugin, which is responsible for introspecting the token. Below is the request to create a client using the oxd endpoint. It will return the `oxd_id`, `client_id` and `client_secret`. You need to use this configurations in the `gluu-oauth-auth` plugin configuration.

          ```bash
          curl -k -X POST https://oxd-server.<gluu-namespace>.svc.cluster.local:8443/register-site \
          -H 'Content-Type: application/json' \
          -d '{
                "op_host": "https://<your_op_host>",
                "redirect_uris": [
                  "https://client.example.com/cb"
                ],
                "client_name": "gluu-oauth-client",
                "scope": [
                  "openid",
                  "oxd"
                ],
                "grant_types": [
                  "client_credentials"
                ]
              }'
          ```

          The second client is for the Kong Consumer. It will return `oxd_id`, `client_id` and `client_secret`. 

          ```bash
          curl -k -X POST https://oxd-server.<gluu-namespace>.svc.cluster.local:8443/register-site \
          -H 'Content-Type: application/json' \
          -d '{
                "op_host": "https://<your_op_host>",
                "redirect_uris": [
                  "https://client.example.com/cb"
                ],
                "client_name": "consumer-client",
                "scope": [
                  "openid",
                  "oxd"
                ],
                "grant_types": [
                  "client_credentials"
                ]
              }'
          ```

    1. Add the following JSON Configuration to the `kong.yml` file.

          ```json
          {
            "_format_version": "1.1",
            "consumers": [
              {
                "custom_id": "<consumer_clients_client_id>",
                "username": "<give_any_unique_name>"
              }
            ],
            "plugins": [
              {
                "config": {
                  "client_id": "<above_oauth_op_clients_client_id>",
                  "client_secret": "<above_oauth_op_clients_client_secret>",
                  "oxd_id": "<above_oauth_op_clients_oxd_id>",
                  "custom_headers": [
                    {
                      "format": "string",
                      "header_name": "x-consumer-id",
                      "value_lua_exp": "consumer.id"
                    },
                    {
                      "format": "jwt",
                      "header_name": "x-oauth-introspect_data",
                      "value_lua_exp": "introspect_data"
                    }
                  ],
                  "op_url": "https://<your_op_host>",
                  "oxd_url": "https://oxd-server.<gluu-namespace>.svc.cluster.local:8443"
                },
                "name": "gluu-oauth-auth",
                "service": "demo-service"
              }
            ],
            "routes": [
              {
                "hosts": [
                  "backend.com"
                ],
                "name": "demo-route",
                "service": "demo-service"
              }
            ],
            "services": [
              {
                "name": "demo-service",
                "url": "<your_upstream_app_url"
              }
            ]
          }
          ```

After starting Kong, you can see configuration in GG UI, but can't edit it. You always need to update the `kong.yml` file. 

In this example, we configured the `gluu-oauth-auth` plugin, and you can similarly configure the all other plugins using `kong.yml`. In the `Plugins` section, you can see the parameters for every plugin.

Check the Kong docs [here](https://docs.konghq.com/2.0.x/db-less-and-declarative-config/) for more details about the Kong DB-less feature.

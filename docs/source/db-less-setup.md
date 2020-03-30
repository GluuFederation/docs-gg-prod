# Kong DB Less Setup

Kong has excellent db less setup where you don't need postgres database. You just need to save the configuration in configuration file, in YAML or JSON, using declarative configuration.

But there is one limitation, You cannot configure it via a GG UI Admin Panel. Kong has no API for DB Less configuration. You need to add all the details by your self in declarative configuration file. Also You need to manually create the OP Clients using the OXD `/register-client` endpoint and pass all the credential to plugin configuration.

For Clustering you need to put same configuration on the all the nodes.

## Configuration

To use Kong in DB-less mode, set the `database` directive of kong.conf to `off`. As usual, you can do this by editing `kong.conf` and setting `database=off` and `declarative_config=/etc/kong/kong.yml`.

### Creating a Declarative Configuration File

To load entities into DB-less Kong, we need a declarative configuration file. Run following command to create configuration file:

```bash
cd /etc/kong
touch kong.yml
```

Let's configure the `gluu-oauth-auth` plugin with DB-Less mode.

1. First Step is to you need to create a OP Client for `gluu-oauth-auth` plugin using the OXD `/register-site` endpoint.

      You need two client one for `gluu-oauth-auth` plugin which responsible for introspect the token. Below is the request to create client using OXD Endpoint. It will return you `oxd_id`, `client_id` and `client_secret`. You need to use this configurations in `gluu-oauth-auth` plugin configuration.
      
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
      
      Second client is for Kong Consumer. It will return `oxd_id`, `client_id` and `client_secret`. For 

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

1. Add the below JSON Configuration in your kong.yml file.

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
            
3. Checking The Declarative Configuration File.

      ```bash
      sudo kong config -c /etc/kong/kong.conf parse /etc/kong/kong.yml
      ```

4. Start Kong

      ```bash
      kong start
      ```

After starting kong, you can see configuration in GG UI but you can't edit it. You always need to update the `kong.yml` file. 

We configured the `gluu-oauth-auth` plugin like wise you can configure the all other plugins using `kong.yml`. In `Plugins` section, you can see the parameters for every plugin.

Check kong docs [here](https://docs.konghq.com/2.0.x/db-less-and-declarative-config/) for more details about the Kong DB Less feature.

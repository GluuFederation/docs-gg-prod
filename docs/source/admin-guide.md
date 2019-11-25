# Gluu Gateway Administration Guide

## Overview

## Configuration

### Service Level

1. Add Service

      Follow these step to add Service using GG UI:
 
      - Click `SERVICES` on the left panel
      - Click on `+ ADD NEW SERVICE` button
      - Fill the form by your upstream service details

1. Add Route

      Route is recommended to reach at kong proxy. Follow these steps to add route:
      
      - Click on `service name` or `edit` button of above added service
      - Click `ROUTES`
      - Click the `+ ADD ROUTE` button
      - Fill the form by routing details. Check kong docs for more routing capabilities [here](https://docs.konghq.com/0.14.x/proxy/#routes-and-matching-capabilities). <!--Fix this step-->

1. Add [Plugins](./plugin/plugin-intro.md)
     
### Route Level

1. Add Service

      Follow these step to add Service using GG UI
 
      - Click [`SERVICES`](../../admin-gui/#services) on the left panel
      - Click on [`+ ADD NEW SERVICE`](../../admin-gui/#add-service) button
      - Fill the form by your upstream service details

1. Add Route

      Follow these steps to add route:
      
      - Click on `service name` or `edit` button of above added service
      - Click [`ROUTES`](../../admin-gui/#routes)
      - Click the [`+ ADD ROUTE`](../../admin-gui/#add-route) button
      - Fill the form by routing details. Check kong docs for more routing capabilities [here](https://docs.konghq.com/0.14.x/proxy/#routes-and-matching-capabilities).

1. Add [Plugins](./plugin/plugin-intro.md)
   
## Logs

Gluu Gateway logs can be found at: 

| Components | Log file path |
|------------|---------------|
| Admin Console(Konga) | `/var/log/konga.log` |
| Kong | `/usr/local/kong/logs` |
| oxd | `/var/log/oxd-server/oxd-server.log` |
| GG Setup logs | `/opt/gluu-gateway/setup/gluu-gateway-setup.log` and `/opt/gluu-gateway/setup/gluu-gateway-setup_error.log` |

!!! Important
    konga.log also shows the curl commands for all API requests to Kong and oxd made by the Konga GUI. You can use this curl command to automate the configuration instead of using the web interface.

Gluu Gateway logs can be found at: 

| Components | Log file path |
|------------|---------------|
| Admin Console(Konga) | `/var/log/konga.log` |
| Kong | `/usr/local/kong/logs` |
| oxd | `/var/log/oxd-server/oxd-server.log` |

!!! Important
    Konga.log also shows the curl commands for all API requests to Kong and oxd made by the Konga GUI. You can use this curl command to automate the configuration instead of using the web interface.

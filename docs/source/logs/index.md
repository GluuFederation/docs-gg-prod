Gluu Gateway logs can be found at: 

| Components | Log file path |
|------------|---------------|
| Admin Console(Konga) | `/var/log/konga.log` |
| Kong | `/usr/local/kong/logs` |
| oxd | `/var/log/oxd-server/oxd-server.log` |

!!! Important
    Konga.log also shows the equivalent curl command that is all the request to Kong API and OXD API made by Konga GUI. You can use this curl command for automate the configuration instead of using the web interface.

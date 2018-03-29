# Configuration

### Gluu Gateway

The Gluu Gateway service used to manage all the gluu-gateway components (Admin GUI Portal - Konga, Kong, Postgres, OXD-Server, OXD-https-extension).

* Gluu-gateway service

```
 # service gluu-gateway [start|stop|restart|status]
```

### Admin GUI Portal - Konga

* You can configure konga by setting properties in local.js file. This is used to set port, oxd, OP and client settings.

```
/opt/gluu-gateway/konga/config/local.js
```

* Konga service

```
# service konga [start|stop|restart|status]
```

### Kong

* You can configure kong by using kong.conf file.

```
/etc/kong/kong.conf
```

* Kong service

```
 # service kong [restart|stop|restart|status]
```

or 

```
 # kong [restart|stop|restart|status]
```

### OXD

* Configure oxd-server

```
/etc/oxd/oxd-server/oxd-conf.json
```

* OXD-Server service

```
 # service oxd-server [start|stop|restart|status]
```

* Configure OXD-https-extension

```
/opt/oxd-https-extension/lib/oxd-https.yml
```

* Start/Stop/Restart/Status oxd-https-extension

```
 # service oxd-https-extension [start|stop|restart|status]
```

### Restore KONG to factory default

Execute below following command in sequence.

1. Stop the kong.

```
# kong stop
```

2. `reset` option is used to reset the configured database. It deleted all the table and data of the configured database.

```
# kong migrations reset
```

3. `up` option is used to create the table in the configured database. Also, execute all missing migrations up to the latest available.

```
# kong migrations up
```

4. Start kong

```
# kong start
```
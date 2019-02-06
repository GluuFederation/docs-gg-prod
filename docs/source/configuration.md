# Configuration

## Gluu Gateway

The Gluu Gateway service is used to manage all components, including: the Admin GUI Portal Konga, Kong, Postgres and OXD Server.

!!! Warning 
    Stopping one service influences the work of the others. Make sure all the required services are up and running to provide a stable Gluu Gateway environment. 

* Manage the Gluu Gateway service:

    ```
     # service gluu-gateway [start|stop|restart|status]
    ```

## Admin GUI Portal - Konga

* Configure Konga by setting properties in the local.js file. This is used to set the port, oxd, OP and client settings.

    ```
     /opt/gluu-gateway/konga/config/local.js
    ```

    |Property|Description|
    |--------|-----------|
    |kong_admin_url|Used to set Kong admin URL|
    |connections|Used to set Postgres DB configuration for Admin GUI|
    |models|Used to set the Database model|
    |session|Session secret is automatically generated when your new app is created. It uses Connect's cookie parser to normalize configuration differences between Express and Socket.io and hooks into Sails' middleware interpreter to allow you to access and auto-save to `req.session` with Socket.io the same way you would with Express.|
    |ssl|Used to set ssl cert for GUI Application. Which provide facility to start application on https|
    |port|Used to set application port|
    |environment|Used to ser project environment. Konga and Gluu Gateway service start application with production environment.|
    |log|Used to set the log level|
    |oxdWeb|Used to set OXD Server URL|
    |opHost|Used to set your OP server URL|
    |oxdId|Used to set the OXD OP client oxd_id which is used for login in GG GUI|
    |clientId|Used to set the OP Client's client id which is used for login in GG GUI|
    |clientSecret|Used to set the OP Client's client secret which is used for login in GG GUI|
    |oxdVersion|Used to set OXD server version|
    |ggVersion|It shows the Gluu Gateway version|
    |explicitHost|Used to define Gluu gateway GUI host explicitly. It by default listens to localhost only for security. You can set it with global IP to access GUI globally.|

    GUI is in `sailsjs-v0.12` node js framework. For more detail configuration take a look on [sailjs documentation](https://0.12.sailsjs.com/documentation/reference/configuration).

* Manage the Konga service

    ```
     # service konga [start|stop|restart|status]
    ```

## Kong

* Configure Kong by using the kong.conf file.

    ```
     /etc/kong/kong.conf
    ```

* Manage the Kong service

    ```
     # service kong [restart|stop|restart|status]
    ```

    or 

    ```
     # kong [restart|stop|restart|status]
    ```

## OXD

Refer to the [oxd docs](https://gluu.org/docs/oxd) for more information on the topics below. 

* Configure the oxd Server

    ```
     /etc/oxd/oxd-server/oxd-conf.json
    ```

* Manage the oxd Server service

    ```
     # service oxd-server [start|stop|restart|status]
    ```

## Restore Kong to factory default

Execute the following sequence of commands:

1. Stop Kong.

    ```
     # kong stop
    ```

1. The `reset` option is used to reset the configured database. It deletes all the tables and data of the configured database.

    ```
     # kong migrations reset
    ```

1. The `up` option is used to create a table in the configured database. It also executes all missing migrations up to the latest available one.

    ```
     # kong migrations up
    ```

1. Start Kong.

    ```
     # kong start
    ```

## Migrate from Dev to Prod 

- Export **kong** and **konga** database from development server.

```
 $ pg_dump --dbname=postgresql://postgres:admin@localhost:5432/konga > konga.sql
 $ pg_dump --dbname=postgresql://postgres:admin@localhost:5432/konga > kong.sql
```

- [Install](./installation) and [Setup](./installation/#run-the-setup-script) GG on your production server.

- Stop OXD server on production server and take OXD db files from your development server from path `/opt/oxd-server/data/` and replace files in production server and Start OXD server `service oxd-server-4.0.beta start`.

- Stop services `service kong stop` and `service konga stop`. Make sure services are stopped.

- Drop `konga` and `kong` database on production server.

```
 $ sudo -iu postgres /bin/bash -c 'psql -c "Drop database konga"'
 $ sudo -iu postgres /bin/bash -c 'psql -c "Drop database kong"'
```

- Create database with name `konga` and `kong`.

```
 $ sudo -iu postgres /bin/bash -c 'psql -c "Create database konga"'
 $ sudo -iu postgres /bin/bash -c 'psql -c "Create database kong"'
```

- Import above sql file in production server.

```
 $ sudo -iu postgres /bin/bash -c "psql konga < absolute_path/konga.sql"
 $ sudo -iu postgres /bin/bash -c "psql kong < absolute_path/kong.sql"
```

- Start services `service kong start` and `service konga stop`. You need to use same Gluu CE in production which your are using in development case otherwise you need to configure plugin in production again with new client credentails.

!!! Info
    `absolute_path` is full path of your file. For Example: your file is in home folder then the path is `/home/konga.sql`.

!!! Note
    You may skip some step as per your requirments.

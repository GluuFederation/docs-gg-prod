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

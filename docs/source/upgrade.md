# Upgrade

## Upgrade from 4.x to 4.2

Follow these steps to upgrade from 4.x to 4.2.

!!! Important
    Upgrades should always be thoroughly scoped and tested on a development environment first.

1. Take a backup of your `Kong` and `Konga` database. Use the following command to export a Database. Make sure your `postgresql` service is started. 

      ```
      pg_dump --dbname=postgresql://<user>:<password>@localhost:5432/konga > konga.sql
      pg_dump --dbname=postgresql://<user>:<password>@localhost:5432/kong > kong.sql
      ```
      
      If you get `Ident authentication failed for user ...` problem then you need to update the file `/var/lib/pgsql/10/data/pg_hba.conf`. Set `host .. .. .. ident` to `host .. .. .. md5`. After this change you need to restart the `service postgresql restart` service.

1. Take a backup of `/opt/gluu-gateway/konga/config/local.js`, `/etc/kong/kong.conf` and `/opt/oxd-server/dadatata/oxd_db.mv.db`. Run the following command.

      ```
      cp /opt/gluu-gateway-ui/config/local.js ./
      cp /opt/oxd-server/data/oxd_db.mv.db ./
      cp /etc/kong/kong.conf ./
      ```
      
1. Stop the Gluu Gateway service

      ```
      service gluu-gateway stop
      service kong stop
      service konga stop
      service oxd-server stop
      ```

1. Uninstall GG 4.x, nodejs, oxd-server and Kong. 

      For Ubuntu 18
      ```
      apt-get purge gluu-gateway kong nodejs oxd-server
      ```
      
      For CentOS 7 and RHEL 7
      ```
      yum remove gluu-gateway kong nodejs oxd-server
      ```

1. We remove oxd from **GG 4.2**. You need to use OXD which is now at your Gluu CE. Put `oxd_db.mv.db` at `/opt/oxd-server/data` in Gluu CE.

1. Install GG 4.2 by following details [here](../installation). Don't run the setup script. Follow the next step for upgrade.

1. Move `konga.sql` and `kong.sql` file to `/opt/gluu-gateway-setup/templates`

      ```
      mv konga.sql kong.sql /opt/gluu-gateway-setup/templates
      ```

1. [Download upgrade script from here](https://raw.githubusercontent.com/GluuFederation/gluu-gateway-setup/version_4.2.0/setup/gg-upgrade-4-1-to-4-2-1.py) and start installation with the following command.
      
      - When asked to `generate client`, select `n` and it will prompt to ask oxd_id.
      - Open a new terminal, open `local.js` which you have take backup in step-2. Open it and copy past the oxd_id, client_id and client_secret.

      ```
      cd /opt/gluu-gateway-setup/
      python gg-upgrade-4-1-to-4-2-1.py
      ```

1. The upgrade is done here. If there are any configuration missing from the config files, update manually. Please check all the services running using command `netstat -ntlp`.

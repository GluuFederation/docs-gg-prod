# Clustering

Gluu Gateway is based on the [Kong Gateway](https://konghq.com/kong/), which supports clustering. Kong's docs about clustering are available [here](https://docs.konghq.com/1.3.x/clustering/).

A Kong cluster can scale the system horizontally by adding more machines/VMs/containers to handle more incoming requests.

To configure multiple Kong nodes into a cluster, point them all to the same datastore or use the same declarative configuration in DB-less mode.

A load balancer is required in front of the Kong cluster to distribute traffic across the available nodes.

## Set up the Gluu Gateway node

### Install Kong

1. Ubuntu 16
    
      - Download Kong 1.3 from [here](https://bintray.com/kong/kong-deb/download_file?file_path=kong-1.3.0.xenial.amd64.deb).
      - 
        ```
        sudo apt-get update
        sudo apt-get install openssl libpcre3 procps perl
        sudo dpkg -i kong-*.deb
        ``` 
      
1. Ubuntu 18
    
      - Download Kong 1.3 from [here](https://bintray.com/kong/kong-deb/download_file?file_path=kong-1.3.0.bionic.amd64.deb).
      - 
        ```
        sudo apt-get update
        sudo apt-get install openssl libpcre3 procps perl
        sudo dpkg -i kong-*.deb
        ``` 

1. RHEL 7
  
      - Download Kong 1.3 from [here](https://bintray.com/kong/kong-rpm/download_file?file_path=rhel/7/kong-1.3.0.rhel7.amd64.rpm)
      - 
        ```
        sudo yum install kong-*.rpm --nogpgcheck
        ```

1. CentOS 7

      - Download Kong 1.3 from [here](https://bintray.com/kong/kong-rpm/download_file?file_path=centos/7/kong-1.3.0.el7.amd64.rpm)
      - 
        ```
        sudo yum install epel-release
        sudo yum install kong-*.rpm --nogpgcheck
        ```

### Set up GG plugins and libraries

- Download [gluu-gateway-node-deps.zip](https://github.com/GluuFederation/gluu-gateway/raw/version_4.0/gluu-gateway-node-deps.zip)

- `unzip gluu-gateway-node-deps.zip`

- `cd gluu-gateway-node-deps`

- `python gg-kong-node-setup.py`

- The script will ask the following questions about the Postgres database, which is shared between the nodes.

    | Questions | Descriptions |
    |-----------|--------------|
    |Enter PG host|Enter your Postgres database host or IP|
    |Enter PG Port|Enter your Postgres database port|
    |Enter PG User|Enter your Postgres database user|
    |Enter PG Password|Enter your Postgres database password. It will not show during typing values, so you just need to type your password.|
    |Enter PG Database|Enter your Postgres database name which you are already sharing between multiple kong nodes.|
    
- Done. If any problems arise, check the `gg-kong-setup-error.log` and `gg-kong-setup.log` log files.

Other Kong configurations are adjusted by updating `/etc/kong/kong.conf`.

<!--
## Deploy Gluu-Gateway node as Docker container

Coming soon. 
-->

# Clustering

Gluu-Gateway has the [kong](http://konghq.com/). Kong is the gateway which provide the proxy endpoint where end-users or client applications request for your Web or API application so for cluster you need to configure and setup the Kong.

A Kong cluster allows you to scale the system horizontally by adding more machines to handle more incoming requests. They will all share the same configuration since they point to the same database. Kong nodes pointing to the same datastore will be part of the same Kong cluster.

**To configure multiple Kong nodes into a cluster, point them all to the same datastore.**

**You need a load-balancer in front of your Kong cluster to distribute traffic across your available nodes.**

## Setup Kong node

### 1. Install Kong

Install Kong from the [Kong official website.](https://konghq.com/install/)

### 2. Setup GG plugins and libs

- Download [gluu-gateway-node-deps.zip](https://github.com/GluuFederation/gluu-gateway/raw/version_4.0/gluu-gateway-node-deps.zip)

- `unzip gluu-gateway-node-deps.zip`

- `cd gluu-gateway-node-deps`

- `python gg-kong-node-setup.py`

- Script will ask you following question about your postgres db. This is the database which your are sharing between the multiple nodes.

    | Questions | Descriptions |
    |-----------|--------------|
    |Enter PG host|Enter your postgres database host or ip|
    |Enter PG Port|Enter your postgres database port|
    |Enter PG User|Enter your postgres database user|
    |Enter PG Password|Enter your postgres database password. It will not show during typing values, so you just need to type your password.|
    |Enter PG Database|Enter your postgres database name which you are already sharing between multiple kong nodes.|
    
- Done. If you get any problem please check the `gg-kong-setup-error.log` and `gg-kong-setup.log` log files.

For more kong configurations, you need to update the `/etc/kong/kong.conf`.

Please check the Kong docs for more details about cluster [here](https://docs.konghq.com/1.3.x/clustering/).

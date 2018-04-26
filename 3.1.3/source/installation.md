# Installation

The istallation is a three-part process:

1. [Add the required third party repositories](#required-third-party-repositories)
2. [Install the `gluu-gateway` package](#install-gluu-gateway-package)
3. [Run `setup-gluu-gateway.py`](#run-setup-script)

### Required Third Party repositories

!!! Note Always run the following commands as root.

* Add the Gluu repo:

```
# echo "deb https://repo.gluu.org/ubuntu/ trusty-devel main" > /etc/apt/sources.list.d/gluu-repo.list
# curl https://repo.gluu.org/ubuntu/gluu-apt.key | apt-key add -
```

* Add openjdk-8 PPA:

```
# add-apt-repository ppa:openjdk-r/ppa
```

* Add the Postgresql-10 repo:

```
# echo "deb http://apt.postgresql.org/pub/repos/apt/ trusty-pgdg main" > /etc/apt/sources.list.d/psql.list
# wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add -
```

* Add the Node repo:

```
# curl -sL https://deb.nodesource.com/setup_8.x | sudo -E bash -
```

### Install the gluu-gateway package

```
 # apt update
 # apt install gluu-gateway
```


### Run the setup script

```
 # cd /opt/gluu-gateway/setup
 # python setup-gluu-gateway.py
```

You will be prompted to answer some questions. Just hit Enter to accept the
default value which is specified in square brackets.

!!! Warning When you are prompted to provide a two-letter value, make sure you follow the instructions. A mistake may result in the lack of certificates.

| **Question** | **Explanation** |
|----------|-------------|
| **Enter IP Address** | IP Address of your API gateway  |
| **Enter Kong hostname** | Internet-facing FQDN to generate certificates and metadata. Do not use an IP address or localhost. |
| **Country** | Used to generate web X.509 certificates |
| **State** | Used to generate web X.509 certificates |
| **City** | Used to generate web X.509 certificates |
| **Organization** | Used to generate web X.509 certificates |
| **Email** | Used to generate web X.509 certificates |
| **Password** | If you already have a database password for user `postgres`, enter it here. Otherwise, enter a new password. |
| **Would you like to configure oxd-server?** | If you already have oxd-web on the network, skip this configuration. |
| **OP hostname** | Used to configure the oxd default OP hostname. Many deployments use a single domain's OP service, so it makes sense to set it as the default. |
| **License Id** | From [oxd-server license](https://oxd.gluu.org/) |
| **Public key** | From [oxd-server license](https://oxd.gluu.org/) |
| **Public password** | From [oxd-server license](https://oxd.gluu.org/) |
| **License password** | From [oxd-server license](https://oxd.gluu.org/) |
| **oxd https url** | Make sure oxd-https-extension is running. |
| **Would you like to generate client_id/client_secret for konga?** | You can register a new OpenID Client or enter existing client credentials manually. You may want to extend the client expiration date on the Gluu Server if you plan to use this service more than one day. If you enter existing client details, then your client must have the `https://localhost:1338` URL entry in Redirect Login URIs and Post Logout Redirect URIs. |
| **oxd_id** | Used to manually set oxd id. |
| **client_id** | Used to manually set client id. |
| **client_secret** | Used to manually set client secret. |

### Finish the setup

```
 Gluu Gateway configuration successful!!! https://localhost:1338
```

If you see the above message, it means the installation was successful. To log in
to the Gluu Gateway admin portal, create an ssh tunnel on port 1338 from your
workstation to the Gluu Gateway server, and point your browser at
`https://localhost:1338`.

# Installation

To install Gluu Gateway, follow these steps:

1. [Add the required third party repositories](#required-third-party-repositories)
2. [Install the `gluu-gateway` package](#install-the-gluu-gateway-package)
3. [Run `setup-gluu-gateway.py`](#run-the-setup-script)

!!! Note
    Before installing Gluu Gateway, be sure to have a [Gluu Server](https://gluu.org/docs/ce) installed and operational. The Gluu Gateway setup script requires an existing Gluu Server's hostname be provided. 

## Minimum Requirements

Gluu Gateway needs to be deployed on a server or VM with the following minimum requirements:

|CPU Unit|RAM |Disk Space|Processor Type|
|--------|--- |----------|--------------|
|1       |2 GB|10 GB     |64 Bit|

## Pre-requirements

Gluu Gateway requires an OAuth 2.0 Authorization Server (AS), typically the Gluu Server, to issue OAuth clients and scopes, and perform client authentication.

Gluu Gateway is compatible with the following versions of Gluu:

- Gluu Server [CE 4.1](https://gluu.org/docs/ce/4.1)

## Required Third Party repositories

!!! Important 
    Always run the following commands as root.

### Ubuntu 18
* Add the Gluu repo:

```
echo "deb https://repo.gluu.org/ubuntu/ bionic main" > /etc/apt/sources.list.d/gluu-repo.list
```

```
curl https://repo.gluu.org/ubuntu/gluu-apt.key | apt-key add -
```

* Add the PostgreSQL 10 repo:

```
echo "deb http://apt.postgresql.org/pub/repos/apt/ bionic-pgdg main" > /etc/apt/sources.list.d/psql.list
```

```
wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add -
```

* Add the Node repo:

```
curl -sL https://deb.nodesource.com/setup_10.x | sudo -E bash -
```

### Ubuntu 16

* Add the Gluu repo:

```
echo "deb https://repo.gluu.org/ubuntu/ xenial main" > /etc/apt/sources.list.d/gluu-repo.list
```

```
curl https://repo.gluu.org/ubuntu/gluu-apt.key | apt-key add -
```

* Add the PostgreSQL 10 repo:

```
echo "deb http://apt.postgresql.org/pub/repos/apt/ xenial-pgdg main" > /etc/apt/sources.list.d/psql.list
```

```
wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add -
```
* Add the Node repo:

```
curl -sL https://deb.nodesource.com/setup_10.x | sudo -E bash -
```

<!--
### Debian 9
* Add the Gluu repo:
```
# echo "deb https://repo.gluu.org/debian/ stretch-testing main" > /etc/apt/sources.list.d/gluu-repo.list
# curl https://repo.gluu.org/debian/gluu-apt.key | apt-key add -
```
* Add the PostgreSQL 10 repo:
```
# echo "deb http://apt.postgresql.org/pub/repos/apt/ stretch-pgdg main" > /etc/apt/sources.list.d/psql.list
# wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add -
```
* Add the Node repo:
```
# curl -sL https://deb.nodesource.com/setup_10.x | sudo -E bash -
```
-->

### CentOS 7

* Add the Gluu repo:

```
wget https://repo.gluu.org/centos/Gluu-centos7.repo -O /etc/yum.repos.d/Gluu.repo
```

```
wget https://repo.gluu.org/centos/RPM-GPG-KEY-GLUU -O /etc/pki/rpm-gpg/RPM-GPG-KEY-GLUU
```

```
rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-GLUU
```

* Add the PostgreSQL 10 repo:

```
rpm -Uvh https://yum.postgresql.org/10/redhat/rhel-7-x86_64/pgdg-redhat-repo-latest.noarch.rpm
```

* Add the Node repo:

```
curl -sL https://rpm.nodesource.com/setup_10.x | sudo -E bash -
```


### RHEL 7

* Add the Gluu repo:

```
wget https://repo.gluu.org/rhel/Gluu-rhel7.repo -O /etc/yum.repos.d/Gluu.repo
```

```
wget https://repo.gluu.org/rhel/RPM-GPG-KEY-GLUU -O /etc/pki/rpm-gpg/RPM-GPG-KEY-GLUU
```

```
rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-GLUU
```

* Add the PostgreSQL 10 repo:

```
rpm -Uvh https://yum.postgresql.org/10/redhat/rhel-7-x86_64/pgdg-redhat-repo-latest.noarch.rpm
```

* Add the Node repo:

```
curl -sL https://rpm.nodesource.com/setup_10.x | sudo -E bash -
```


## Install the Gluu Gateway package

!!! important
    The Gluu Gateway package installs the following required components: PostgreSQL v10, oxd Server 4.1, NodeJS v8, Kong Community Edition v0.14.1.

### Ubuntu 16, 18

```
apt update
```

```
apt install gluu-gateway
```

### Centos 7, RHEL 7

```
yum clean all
```

```
yum install gluu-gateway
```

## Run the setup script

!!! Important 
    Before start setup, stop your all services which run on ports 443, 8443, 1338, 8000 and 8001. 

!!! Info
    If you are behind the corporate proxy, you need to export `HTTP_PROXY`, `HTTPS_PROXY`. [More details](https://github.com/GluuFederation/gluu-gateway/issues/352)

```
cd /opt/gluu-gateway-setup
```

```
python setup-gluu-gateway.py
```

After acknowledging the Gluu Stepped-Up Support License, you will be prompted to answer several questions. Just hit Enter to accept the default values, which are specified in square brackets.

!!! Important 
    When prompted to provide a two-letter value, make sure to follow the instructions. A mistake may result in the lack of certificates.

| **Question** | **Explanation** |
|----------|-------------|
| **Enter IP Address** | IP Address of your API gateway  |
| **Enter Hostname** | Internet-facing FQDN to generate certificates and metadata. Do not use an IP address or localhost. |
| **Enter two-letter Country Code** | Used to generate web X.509 certificates |
| **Enter two-letter State Code** | Used to generate web X.509 certificates |
| **Enter your City or locality** | Used to generate web X.509 certificates |
| **Enter Organization name** | Used to generate web X.509 certificates |
| **Enter Email Address** | Used to generate web X.509 certificates |
| **Password** | If you already have a postgres database password for user `postgres`, enter it here. Otherwise, enter a new password. |
| **OP Server Host** | The hostname of the Gluu Server that will be used for OAuth 2.0 client credentials and access management. **Example**: your-op.server.com |
| **Install OXD Server?** | If you choose Y(yes) then it will install fresh oxd server in your machine. If you choose N(No) then it will ask you next question `Enter your existing OXD server URL`, where you need to enter your existing oxd server URL. Check [here](https://gluu.org/docs/oxd/4.1/) for more details about oxd server. | 
| **OXD Server URL** | If oxd is installed on a different hostname than Gluu Gateway, provide its URL. If not, enter the hostname for Gluu Gateway|
| **Generate client credentials to call oxd-server API's?** | Register an OpenID Client for Konga, or enter existing client credentials manually. Take care about your Client at your OP server side; make sure to [extend this expiration date](https://www.gluu.org/docs/oxd/4.1/faq/#client-expires-how-can-i-avoid-it). It will create a client with the `openid`, `oxd`, `permission` and `username` scope. You need to enable all this scope for dynamic client registration so that it will include during client creation. In case if missing then you can add this scope after setup complete using [Gluu oxTrust UI](https://gluu.org/docs/ce/4.1/admin-guide/openid-connect/#dynamic-client-registration). If you enter existing client details, make sure your client in Redirect Login URIs and Post Logout Redirect URIs field has the value `https://localhost:1338`.|
| **OXD Id** | Used to manually set the oxd ID. |
| **Client Id** | Used to manually set the client ID. |
| **Client Secret** | Used to manually set the client secret. |

## Finish the setup

```
Gluu Gateway configuration successful!!! https://localhost:1338
```

If you see the above message, it means the installation was successful. To log in to the Gluu Gateway admin portal, create an SSH tunnel on port 1338 to the Gluu Gateway server, and point the browser at `https://localhost:1338`. Use the login and password used to access the Gluu Server.

At Linux terminal you can create the tunnel as:

```
ssh -L 1338:localhost:1338 user@your_linux_host

```

!!! Important
    To diagnose errors during setup, check the log files: `/opt/gluu-gateway-setup/gluu-gateway-setup.log` and `/opt/gluu-gateway/setup/gluu-gateway-setup_error.log`
    
!!! Note
    If you do not want an SSH tunnel connection, see the [FAQ](./faq.md#how-can-i-change-the-listening-address-and-port) for global access configuration. After these settings, you also need to update the OP clients redirect URL and post logout URL using the oxd [update-site](https://gluu.org/docs/oxd/4.1/api/#update-site) API.
    
If automating installation, the JSON values can be passed directly to the setup script. The JSON values can be found [here.](https://github.com/GluuFederation/gluu-gateway/blob/version_4.1/t/scripts/install.sh#L64)

## Applications and their ports

| Port | Description |
|------|-------------|
|1338| Gluu Gateway Admin GUI|
|8001|Kong Admin API|
|8000|Kong Proxy Endpoint|
|443|Kong SSL Proxy Endpoint. By default, Kong uses 8443 port for SSL proxy, but during setup it is changed to 443.|
|8443|oxd Server|

To change the port and configurations, you can update the specific application's config file. For details, see the [Configuration](./configuration.md) section.

<!---

## Upgrade

When a Gluu Gateway upgrade is available, follow these steps:

* Add the Gluu repo:
```
# echo "deb https://repo.gluu.org/ubuntu/ xenial-devel main" > /etc/apt/sources.list.d/gluu-repo.list
```
* Run:
```
 # apt update
 # apt install --only-upgrade gluu-gateway
```
* Run the setup script:
```
 # cd /opt/gluu-gateway/setup
 # python setup-gluu-gateway.py
```
!!! Important 
    During setup, choose to enter the existing client credentials manually if you want to continue using them. If you want to start fresh, choose to generate client credentials again. If you do so, you might lose your previously created Services or Consumers.
    
* [Finish](#finish-the-setup) the setup

--->

## Removal

!!! Important 
    Always run the following commands as root.

Choose one of the following three options:

1. Use `apt-get` to remove only the gluu-gateway package. Any dependencies installed at the same time, as well as configuration, will remain.

    ```
    apt-get remove gluu-gateway
    ```

1. If you want to also remove the configuration files (/etc/init.d/gluu-gateway and /opt/gluu-gateway/konga/config), use `purge`.

    ```    	
    apt-get purge gluu-gateway
    ```

1. Using the `--auto-remove` parameter will also remove the package dependencies (kong-community-edition, lua-cjson, nodejs, oxd-server, postgresql).

    ```
    apt-get remove --auto-remove gluu-gateway
    ```

!!! Info
    To remove all other GGs sub components, use `apt-get purge gluu-gateway kong* postgresql-* oxd-server* node*`

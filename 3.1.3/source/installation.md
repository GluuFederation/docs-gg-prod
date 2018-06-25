# Installation

The installation is a three-part process:

1. [Add the required third party repositories](#required-third-party-repositories)
2. [Install the `gluu-gateway` package](#install-the-gluu-gateway-package)
3. [Run `setup-gluu-gateway.py`](#run-the-setup-script)

!!! Note
    Before proceeding with Gluu Gateway installation, you should have a [Gluu Server](https://gluu.org/docs/ce) installed and operational. You will be asked to input the hostname of your Gluu Server while running the Gluu Gateway setup script. 

## Required Third Party repositories

!!! Note 
    Always run the following commands as root.

### Ubuntu 14
* Add the Gluu repo:
```
# echo "deb https://repo.gluu.org/ubuntu/ trusty main" > /etc/apt/sources.list.d/gluu-repo.list
# curl https://repo.gluu.org/ubuntu/gluu-apt.key | apt-key add -
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

<!--- 

### Ubuntu 16
* Add the Gluu repo:
```
# echo "deb https://repo.gluu.org/ubuntu/ xenial-devel main" > /etc/apt/sources.list.d/gluu-repo.list
# curl https://repo.gluu.org/ubuntu/gluu-apt.key | apt-key add -
```
* Add the Postgresql-10 repo:
```
# echo "deb http://apt.postgresql.org/pub/repos/apt/ xenial-pgdg main" > /etc/apt/sources.list.d/psql.list
# wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add -
```
* Add the Node repo:
```
# curl -sL https://deb.nodesource.com/setup_8.x | sudo -E bash -
```

### Debian 8
* Add the Gluu repo:
```
# echo "deb https://repo.gluu.org/debian/ testing main" > /etc/apt/sources.list.d/gluu-repo.list
# curl https://repo.gluu.org/debian/gluu-apt.key | apt-key add -
```
* Add the Postgresql-10 repo:
```
# echo "deb http://apt.postgresql.org/pub/repos/apt/ jessie-pgdg main" > /etc/apt/sources.list.d/psql.list
# wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add -
```
* Add the Node repo:
```
# curl -sL https://deb.nodesource.com/setup_8.x | sudo -E bash -
```

### Debian 9
* Add the Gluu repo:
```
# echo "deb https://repo.gluu.org/debian/ stretch-testing main" > /etc/apt/sources.list.d/gluu-repo.list
# curl https://repo.gluu.org/debian/gluu-apt.key | apt-key add -
```
* Add the Postgresql-10 repo:
```
# echo "deb http://apt.postgresql.org/pub/repos/apt/ stretch-pgdg main" > /etc/apt/sources.list.d/psql.list
# wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add -
```
* Add the Node repo:
```
# curl -sL https://deb.nodesource.com/setup_8.x | sudo -E bash -
```

### CentOS 6
* Add the Gluu repo:
```
# wget https://repo.gluu.org/centos/Gluu-centos-testing.repo -O /etc/yum.repos.d/Gluu.repo
# wget https://repo.gluu.org/centos/RPM-GPG-KEY-GLUU -O /etc/pki/rpm-gpg/RPM-GPG-KEY-GLUU
# rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-GLUU
```
* Add the Postgresql-10 repo:
```
# rpm -Uvh https://yum.postgresql.org/10/redhat/rhel-6-x86_64/pgdg-redhat10-10-2.noarch.rpm
```
* Add the Node repo:
```
curl -sL https://rpm.nodesource.com/setup_8.x | sudo -E bash -
```

### CentOS 7
* Add the Gluu repo:
```
# wget https://repo.gluu.org/centos/Gluu-centos-7-testing.repo -O /etc/yum.repos.d/Gluu.repo
# wget https://repo.gluu.org/centos/RPM-GPG-KEY-GLUU -O /etc/pki/rpm-gpg/RPM-GPG-KEY-GLUU
# rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-GLUU
```
* Add the Postgresql-10 repo:
```
# rpm -Uvh https://yum.postgresql.org/10/redhat/rhel-7-x86_64/pgdg-centos10-10-2.noarch.rpm
```
* Add the Node repo:
```
curl -sL https://rpm.nodesource.com/setup_8.x | sudo -E bash -
```

### RHEL 6
* Add the Gluu repo:
```
# wget https://repo.gluu.org/rhel/Gluu-rhel-testing.repo -O /etc/yum.repos.d/Gluu.repo
# wget https://repo.gluu.org/rhel/RPM-GPG-KEY-GLUU -O /etc/pki/rpm-gpg/RPM-GPG-KEY-GLUU
# rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-GLUU
```
* Add the Postgresql-10 repo:
```
# rpm -Uvh https://yum.postgresql.org/10/redhat/rhel-6-x86_64/pgdg-redhat10-10-2.noarch.rpm
```
* Add the Node repo:
```
curl -sL https://rpm.nodesource.com/setup_8.x | sudo -E bash -
```

### RHEL 7
* Add the Gluu repo:
```
# wget https://repo.gluu.org/rhel/Gluu-rhel-7-testing.repo -O /etc/yum.repos.d/Gluu.repo
# wget https://repo.gluu.org/rhel/RPM-GPG-KEY-GLUU -O /etc/pki/rpm-gpg/RPM-GPG-KEY-GLUU
# rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-GLUU
```
* Add the Postgresql-10 repo:
```
# rpm -Uvh https://yum.postgresql.org/10/redhat/rhel-7-x86_64/pgdg-centos10-10-2.noarch.rpm
```
* Add the Node repo:
```
curl -sL https://rpm.nodesource.com/setup_8.x | sudo -E bash -
```

--->

## Install the gluu-gateway package

### Ubuntu 14

```
 # apt update
 # apt install gluu-gateway
```

<!----
### Ubuntu 14, Ubuntu 16, Debian 8, Debian 9
```
 # apt update
 # apt install gluu-gateway
```

### CentOS 6, Centos 7, RHEL 6, RHEL 7
```
 # yum clean all
 # yum install gluu-gateway
```
--->

## Run the setup script

```
 # cd /opt/gluu-gateway/setup
 # python setup-gluu-gateway.py
```

After acknowledging that the use of the Gluu Gateway is under the MIT license, you will be prompted to answer several questions. Just hit Enter to accept the default values, which are specified in square brackets.

!!! Warning 
    When you are prompted to provide a two-letter value, make sure you follow the instructions. A mistake may result in the lack of certificates.

| **Question** | **Explanation** |
|----------|-------------|
| **Enter IP Address** | IP Address of your API gateway  |
| **Enter Kong hostname** | Internet-facing FQDN to generate certificates and metadata. Do not use an IP address or localhost. |
| **Enter two-letter Country Code** | Used to generate web X.509 certificates |
| **Enter two-letter State Code** | Used to generate web X.509 certificates |
| **Enter your City or locality** | Used to generate web X.509 certificates |
| **Enter Organization name** | Used to generate web X.509 certificates |
| **Email address** | Used to generate web X.509 certificates |
| **Password** | If you already have a database password for user `postgres`, enter it here. Otherwise, enter a new password. |
| **Would you like to configure oxd-server?** | Enables integration with the Gluu Server. |
| **OP hostname** | The hostname of the Gluu Server that will be used for OAuth 2.0 client credentials and access management. |
| **License Id** | From [oxd-server license](https://oxd.gluu.org/) |
| **Public key** | From [oxd-server license](https://oxd.gluu.org/) |
| **Public password** | From [oxd-server license](https://oxd.gluu.org/) |
| **License password** | From [oxd-server license](https://oxd.gluu.org/) |
| **oxd https url** | Make sure the oxd-https-extension is running. |
| **Would you like to generate client_id/client_secret for konga?** | Register an OpenID Client for Konga, or enter existing client credentials manually. By default, the client expiration is set to 24 hours; make sure to extend this expiration date in the Gluu Server. If you enter existing client details, make sure your client in Redirect Login URIs and Post Logout Redirect URIs field, you have the value `https://localhost:1338`. |
| **oxd_id** | Used to manually set oxd id. |
| **client_id** | Used to manually set client id. |
| **client_secret** | Used to manually set client secret. |

## Finish the setup

```
 Gluu Gateway configuration successful!!! https://localhost:1338
```

If you see the above message, it means the installation was successful. To log into
the Gluu Gateway admin portal, create an SSH tunnel on port 1338 from your
workstation to the Gluu Gateway server, and point your browser at
`https://localhost:1338`.

!!! Note
    See [FAQ](./faq.md#how-can-i-change-the-listening-address-and-port) for global access configuration.

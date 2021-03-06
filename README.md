# samba-centos-7
All the packages needed to compile samba4

## PART 1 (Updates)
- Update your system to the latest version. With root user use the command `yum update`.
- Install the EPEL Release Repo. With root user use the command `yum install epel-release -y`.
- Install the Centos Software Collections repo. With root user use the command `yum install centos-release-scl`.
- Samba needs pyhton 3. Install Python 3.6 with root use use the command `yum install rh-python36`.
- Enable python 3.6. With root user use the command `scl enable rh-python36 bash`.

## Part 2 (Disable Selinux & Firewalld & Virtualization Service)
Check Selinux with `sestatus`
To permanently disable SELinux on your CentOS 7 system:
- Open the `/etc/selinux/config` file and set the SELINUX mod to disabled.
```
    # This file controls the state of SELinux on the system.
    # SELINUX= can take one of these three values:
    #       enforcing - SELinux security policy is enforced.
    #       permissive - SELinux prints warnings instead of enforcing.
    #       disabled - No SELinux policy is loaded.
    SELINUX=disabled
    # SELINUXTYPE= can take one of these two values:
    #       targeted - Targeted processes are protected,
    #       mls - Multi Level Security protection.
    SELINUXTYPE=targeted`
```
- To permanently disable firewalld
```
    # systemctl stop firewalld
    # systemctl disable firewalld
```
- To permanently disable virtualization service
```
    # systemctl stop libvirtd.service
    # systemctl disable libvirtd.service
```

## Part 3 (install webmin)
(info --> http://www.webmin.com/rpm.html )
- Create the `/etc/yum.repos.d/webmin.repo` file containing 
```
[Webmin]
name=Webmin Distribution Neutral
#baseurl=https://download.webmin.com/download/yum
mirrorlist=https://download.webmin.com/download/yum/mirrorlist
enabled=1
```
- You should also fetch and install my GPG key with which the packages are signed, with the commands:
```
wget http://www.webmin.com/jcameron-key.asc
rpm --import jcameron-key.asc
```
- Install with command `yum install webmin`


- Restart your CentOS 7 system `shutdown -r now`

## Part 4 (Install Bind DNS)
As root run the command `yum install bind bind-utils -y`
Restart the service `service named restart`

NOTE That:
- Addresses and Topology: Set your Lan Address.
- Zone Defaults: Allow queries from your network. Like 192.168.0.0/24


## Part 5 (Install All the required packages)

```
 yum install attr bind-utils docbook-style-xsl gcc gdb krb5-workstation \
       libsemanage-python libxslt perl perl-ExtUtils-MakeMaker \
       perl-Parse-Yapp perl-Test-Base pkgconfig policycoreutils-python \
       python2-crypto gnutls-devel libattr-devel keyutils-libs-devel \
       libacl-devel libaio-devel libblkid-devel libxml2-devel openldap-devel \
       pam-devel popt-devel python-devel readline-devel zlib-devel systemd-devel \
       lmdb-devel jansson-devel gpgme-devel pygpgme libarchive-devel
```
Check --> https://wiki.samba.org/index.php/Package_Dependencies_Required_to_Build_Samba

```
yum install gpgme-devel jansson-devel lmdb-devel gcc avahi-devel cups-devel dbus-devel docbook-style-xsl e2fsprogs-devel gawk gnupg2 gpgme-devel jansson-devel krb5-devel libacl-devel libaio-devel libarchive-devel libattr-devel libcap-devel libcmocka-devel libtirpc-devel libuuid-devel libxslt ncurses-devel openldap-devel pam-devel perl-generators perl popt-devel quota-devel readline-devel sed xfsprogs-devel xz zlib-devel pkgconfig libblkid-devel gnutls-devel python-devel gdb krb5-workstation setroubleshoot-server setroubleshoot-plugins policycoreutils-python libsemanage-python setools-libs libpcap-devel sqlite-devel libidn-devel libsepol-devel libxml2-devel keyutils-libs-devel cyrus-sasl-devel bind-utils bzip2 vim wget -y
```

## Part 6 (Download and Build)
- To download samba use the command `wget https://download.samba.org/pub/samba/stable/samba-4.10.2.tar.gz`
- To unzip samba use the command `tar -zxvf samba-4.10.2.tar.gz`
- Go to Samba folder `cd samba-4.10.2`
- Configure Samba with 

```
scl enable rh-python36 bash
./configure --prefix=/usr --sysconfdir=/etc --localstatedir=/var --enable-fhs

or

PYTHON=python2 ./configure --prefix=/usr --sysconfdir=/etc --localstatedir=/var --enable-fhs
PYTHON=python2 make
PYTHON=python2 make install

or

PYTHON=python2 ./configure --prefix=/usr --localstatedir=/var --with-configdir=/etc/samba --libdir=/usr/lib64 --with-modulesdir=/usr/lib64/samba --with-pammodulesdir=/lib64/security --with-lockdir=/var/lib/samba --with-logfilebase=/var/log/samba --with-piddir=/run/samba --with-privatedir=/etc/samba --enable-fhs
PYTHON=python2 make
PYTHON=python2 make install

or

PYTHON=python2 ./configure && PYTHON=python2 make && PYTHON=python2 make install

```

Install or update samba libs:
```
yum install samba-libs
```


Samba Tools:
```
/usr/local/samba/bin/samba-tool domain provision --realm=mydomain.lan --domain=mydomain --adminpass='your_password' --server-role=dc --dns-backend=BIND9_DLZ
```

```
./configure --prefix=/usr --localstatedir=/var --with-configdir=/etc/samba --libdir=/usr/lib64 --with-modulesdir=/usr/lib64/samba --with-pammodulesdir=/lib64/security --with-lockdir=/var/lib/samba --with-logfilebase=/var/log/samba --with-piddir=/run/samba --with-privatedir=/etc/samba --enable-cups --with-acl-support --with-ads --with-automount --enable-fhs --with-pam --with-quotas --with-shared-modules=idmap_rid,idmap_ad,idmap_hash,idmap_adex --with-syslog --with-utmp --with-dnsupdate
```
## Part 7 (Create symbolic link)
```
ln -s /usr/local/samba/sbin/samba /usr/sbin/samba
ln -s /usr/local/samba/bin/samba-tool /usr/bin/samba-tool
ln -s /usr/local/samba/sbin/samba_dnsupdate /usr/sbin/samba_dnsupdate
ln -s /usr/local/samba/sbin/samba_upgradedns /usr/sbin/samba_upgradedns
```
## PART 8 (Fix BIND DNS)
During the domain provisioning, join, or classic upgrade, the /usr/local/samba/bind-dns/named.conf file has been created. 
Edit the file named.conf and add the named.conf that samba created
```
vi /etc/named.conf
include "/usr/local/samba/bind-dns/named.conf";
```

update setings
```
samba_upgradedns --dns-backend=BIND9_DLZ
samba_dnsupdate --verbose

```
Restart BIND DNS
```
systemctl restart named
```

Start SAMBA
```
systemctl start samba
```

## Part 9 (Create Systemd File, Samba TMP conf and rename krb5.conf)
- Run the command `vi /usr/lib/systemd/system/samba.service` and add:
```
[Unit]
Description=Samba AD Daemon
Wants=network-online.target
After=network.target network-online.target rsyslog.service

[Service]
Type=forking
PIDFile=/run/samba/samba.pid
LimitNOFILE=16384
ExecStart=/usr/sbin/samba --daemon
ExecReload=/bin/kill -HUP $MAINPID

[Install]
WantedBy=multi-user.target
```
- Run the command `vi /etc/tmpfiles.d/samba.conf` and add:
```
d /var/run/samba 0755 root root -
```
- Run the command `mv /etc/krb5.conf /etc/krb5.conf.old`

## Part 10 Setun Samba Autostart
- Run the command `vi /etc/systemd/system/samba.service` and add the following
```
[Unit]
Description= Samba 4 Active Directory
After=syslog.target
After=network.target

[Service]
Type=forking
PIDFile=/usr/local/samba/var/run/samba.pid
ExecStart=/usr/local/samba/sbin/samba

[Install]
WantedBy=multi-user.target
```

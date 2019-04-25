# samba-centos-7
All the packages needed to compile samba4

## PART 1 (Updates)
- Update your system to the latest version. With root user use the command `yum update`.
- Install the EPEL Release Repo. With root user use the command `yum install epel-release -y`.

## Part 2 (Disable Selinux) & Firewalld
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


- Restart your CentOS 7 system `shutdown -r now`

## Part 3 (Install All the required packages
```
yum install gpgme-devel jansson-devel lmdb-devel gcc avahi-devel cups-devel dbus-devel docbook-style-xsl e2fsprogs-devel gawk gnupg2 gpgme-devel jansson-devel krb5-devel libacl-devel libaio-devel libarchive-devel libattr-devel libcap-devel libcmocka-devel libtirpc-devel libuuid-devel libxslt ncurses-devel openldap-devel pam-devel perl-generators perl popt-devel quota-devel readline-devel sed xfsprogs-devel xz zlib-devel pkgconfig libblkid-devel gnutls-devel python-devel gdb krb5-workstation setroubleshoot-server setroubleshoot-plugins policycoreutils-python libsemanage-python setools-libs libpcap-devel sqlite-devel libidn-devel libsepol-devel libxml2-devel keyutils-libs-devel cyrus-sasl-devel bind-utils bzip2 vim wget -y
```

## Part 4 (Download and Build)
- To download samba use the command `wget https://download.samba.org/pub/samba/stable/samba-4.9.3.tar.gz`
- To unzip samba use the command `tar -zxvf samba-4.9.3.tar.gz`
- Configure Samba with 
```
./configure --prefix=/usr --localstatedir=/var --with-configdir=/etc/samba --libdir=/usr/lib64 --with-modulesdir=/usr/lib64/samba --with-pammodulesdir=/lib64/security --with-lockdir=/var/lib/samba --with-logfilebase=/var/log/samba --with-piddir=/run/samba --with-privatedir=/etc/samba --enable-cups --with-acl-support --with-ads --with-automount --enable-fhs --with-pam --with-quotas --with-shared-modules=idmap_rid,idmap_ad,idmap_hash,idmap_adex --with-syslog --with-utmp --with-dnsupdate
```


## Part 5 (Create Systemd File, Samba TMP conf and rename krb5.conf)
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

## Part 6 Setun Samba Domain Provision
- Run the command `samba-tool domain provision`

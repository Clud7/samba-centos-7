# samba-centos-7
All the packages needed to compile samba4

## PART 1 (Updates)
- Update your system to the latest version. With root user use the command `yum update`.
- Install the EPEL Release Repo. With root user use the command `yum install epel-release -y`.

## Part 2 (Disable Selinux)
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
- Restart your CentOS 7 system `shutdown -r now`


## Part 3 (Download and Build)
- To download samba use the command `wget https://download.samba.org/pub/samba/stable/samba-4.9.3.tar.gz`
- To unzip samba use the command `tar -zxvf samba-4.9.3.tar.gz`

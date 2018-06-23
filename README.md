# Nginx Configuration
## Summary
This is a fully developed Nginx configuration ready for deployment in
production environments. It is pre-configured to be scalable, efficient, and
reliable.

### Author's Notes
[@carlbennett](https://github.com/carlbennett) wanted an nginx configuration
that was both secure and modular enough that it could be put on any server,
with minor tuning to just a few settings to make it work anywhere. And thus,
this configuration was born.

It is based on the Fedora 21 x86\_64 nginx package and has been maintained at
[carlbennett/nginx-conf](https://github.com/carlbennett/nginx-conf) since. It
is fully compatible with Fedora 25 x86\_64 and CentOS 7.3 x86\_64.

Recommended Nginx version: `1.10.2` or newer.

### Glyph's Notes

Being optimized for Laravel installation.

## Features
- Global caching
  - If included, tunes nginx to have browsers cache static resources.
- Global Gzip compression
  - If included, common types of static resources will be compressed by nginx.
- Global URL filtering
  - If included, nginx will disconnect common types of attacks based on the
    URL, instead of responding with an error page or content which could alert
    the bad actor about your server, which would send an invite to come back
    later.
  - Can be extended upon very easily to block even more types of URLs.
- PHP support
  - If included, you can _and should_ define php error reporting and short tags
    in your server block.
  - You can pass other php options via the PHP\_VALUE parameter too.

## Installation
These steps have been tested on Fedora 25 x86\_64, and may require minor
changes to work on non-RHEL systems.

The following commands assume you are logged in as `root` or are `sudo`ing as
`root` before every command.

### Install nginx
:warning: If you are using CentOS, substitute `dnf` with `yum` in the command
below.

```sh
dnf install nginx
```

### Setup the user and group
If you wish to replace apache:
```sh
userdel -r apache
usermod -u 48 nginx
groupmod -g 48 nginx
```

Add permission group for web content:
```
groupadd -r www-data
usermod -aG www-data nginx
usermod -aG www-data `whoami`
```

### Clone this repository
```sh
cd ~
git clone git@github.com:carlbennett/nginx-conf.git
```

### Copy files to system
```sh
cp -r ./etc/nginx/ /etc/nginx
mkdir -p /opt/carlbennett/nginx-www && \
  cp -r ./opt/carlbennett/nginx-www/ /opt/carlbennett/nginx-www
```

### File and directory permissions
```sh
chown -R root:root /etc/nginx
chown -R nginx:www-data /opt/carlbennett/nginx-www
find /opt/carlbennett/nginx-www -type f -print0 | sudo xargs -0 chmod 664
find /opt/carlbennett/nginx-www -type d -print0 | sudo xargs -0 chmod 775
```

### SELinux permissions
```sh
dnf install policycoreutils-python-utils
semanage fcontext -a -t httpd_sys_content_t '/opt/carlbennett/nginx-www(/.*)?'
restorecon -r /etc/nginx /opt/carlbennett/nginx-www
```

### Configure nginx
You should now configure everything under `/etc/nginx` to your liking.

### Run nginx
```sh
systemctl enable nginx
systemctl start nginx
```

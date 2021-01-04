---
title: Unbound with local energized blacklist and TLS forward to NextDNS
date: 2021-01-07 07:40:57
tags:
---
## Working configuration for download
![](unbound.conf)
## Install Unbound on FREEBSD
{% codeblock line_number:false highlight:false lang:bash %}
pkg install unbound
{% endcodeblock %}

## Edit configuration - all below entries go under server: directive
{% codeblock line_number:false highlight:false lang:bash %}
vim /usr/local/etc/unbound/unbound.conf
{% endcodeblock %}

### Configure chroot
{% codeblock line_number:false highlight:false lang:bash %}
directory: /usr/local/etc/unbound
chroot: /usr/local/etc/unbound
{% endcodeblock %}
### TLS cert bundle
{% codeblock line_number:false highlight:false lang:bash %}
tls-cert-bundle: /usr/local/share/certs/ca-root-nss.crt
{% endcodeblock %}

### Caching TTLs
{% codeblock line_number:false highlight:false lang:bash %}
cache-min-ttl: 72000
cache-max-ttl: 86400
{% endcodeblock %}
### Local blacklist zone file
Download energized list from https://github.com/EnergizedProtection/block
{% codeblock line_number:false highlight:false lang:bash %}
wget https://block.energized.pro/ultimate/formats/unbound.conf
{% endcodeblock %}
*Note:* - blacklist needs tweaking i.e. some entries need to be removed
{% codeblock line_number:false highlight:false lang:bash %}
include: /usr/local/etc/unbound/energized-ultimate.blacklist
{% endcodeblock %}

### Forwarding address
{% codeblock line_number:false highlight:false lang:bash %}
forward-zone:
    name:"."
    forward-tls-upstream: yes
    forward-addr: 45.90.28.0@853#<yourcode>.dns1.nextdns.io
{% endcodeblock %}

## Enable remote control
### In its own remote-control: section
{% codeblock line_number:false highlight:false lang:bash %}
remote-control:
    control-enable: yes
    control-use-cert: no
{% endcodeblock %}
## Controling unbound
### restart
{% codeblock line_number:false highlight:false lang:bash %}
service unbound restart
{% endcodeblock %}

### flush zone
{% codeblock line_number:false highlight:false lang:bash %}
unbound-control flush_zone youtube.com
{% endcodeblock %}

### dump cache
{% codeblock line_number:false highlight:false lang:bash %}
unbound-control dump_cache
{% endcodeblock %}



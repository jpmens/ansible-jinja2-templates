## Intro

This trivial example demonstrates how we can use a file with data in YAML format to produce a configuration file. Those of you who've been to my Ansible classes will recognize this as well as my use of the _figlet_ utility, one vastly more important and useful than the stupid _cowsay_ ...

## hostlist.yml

```yaml
---
hostlist:
  - { host: dbserver.example.net, ip: 10.0.1.9 }
  - { host: webserver, ip: 192.168.162.182 }
  - { host: www.example.com, ip: 10.0.1.2 }
```

## Template: `hosts-figlet.j2`

```jinja2
{{ lookup('pipe', 'figlet hosts') | comment }}

{% for h in hostlist %}
{# -15s is a string, 15 wide, left-justified, padded with spaces #}
{{ "%-15s" | format(h.ip) }} {{ h.host }}
{% endfor %}
```

## Output

```txt
#
#  _               _       
# | |__   ___  ___| |_ ___ 
# | '_ \ / _ \/ __| __/ __|
# | | | | (_) \__ \ |_\__ \
# |_| |_|\___/|___/\__|___/
#

10.0.1.9        dbserver.example.net
192.168.162.182 webserver
10.0.1.2        www.example.com
```

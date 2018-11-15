## Intro

This example show how you can loop over hostvars from servers in a group, to configure the nodes in a cluster consistently on every node. For this example to work, all hosts need to be included in the current `ansible-playbook` run, or hostvar caching needs to be enabled.

## inventory.ini

```ini
[cluster]
leader01.example.com cluster_interface=ansible_enp0s31f6
leader02.example.com cluster_interface=ansible_enp0s31f6
leader03.example.com cluster_interface=ansible_enp0s31f6
```

## Template: `cluster-config.j2`

```jinja2
{{ lookup('pipe', 'figlet cluster') | comment }}

cluster_members = "{{ groups['cluster'] | map('extract', hostvars, [cluster_interface,'ipv4','address']) | list | join(',') }}"
```

## Output

```txt
#
#       _           _            
#   ___| |_   _ ___| |_ ___ _ __ 
#  / __| | | | / __| __/ _ \ '__|
# | (__| | |_| \__ \ ||  __/ |   
#  \___|_|\__,_|___/\__\___|_|
#

cluster_members = "10.1.16.32,10.1.16.33,10.1.16.34"
```

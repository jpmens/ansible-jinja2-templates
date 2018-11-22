## Intro

In this example, I show you how we can generate a proxy bash script to be stored in the `/etc/profile.d` directory that is loaded upon startup. The http and https proxy server names can be given as variables to `http_proxy` and `https_proxy` respectively. The variable `no_proxy` contains a list of hosts or IP addresses that will be excluded from the proxy servers. I usually apply the below shown playbook on my servers to configure the proxy settings for individual users.


## Template: `proxy.sh.j2`

```jinja2
#!/bin/bash

export http_proxy={{ http_proxy }}
export https_proxy={{ http_proxy }}
{% if no_proxy is defined -%}
export no_proxy={{ no_proxy | join(',')}}
{%- endif %}
```


## Set Proxy Task: `example_playbook.yml`

```yaml
- name: apply my great playbook on my hosts
  hosts: [a-bunch-of-host]
  become: true
  vars:
    http_proxy: http://httpproxyserver.something.com:3128
    https_proxy: http://httpsproxyserver.something.com:3128
    no_proxy:
      - localhost
      - your.domain.com
      - her.domain.net
      - his.domain.org
      - 10.0.1.0
      - 10.10.1.10
      - somethingelse.com
      # ...

  tasks:

    - name: some tasks before
      # ...

    - name: set proxy script
      template:
        src: templates/proxy.sh.j2
        dest: /etc/profile.d/proxy.sh
        owner: root
        group: root
        mode: 0644

    - name: some tasks after
      # ...

  environment:
    http_proxy: "{{ http_proxy }}"
    https_proxy: "{{ https_proxy }}"
```


## Content of `/etc/profile.d/proxy.sh`

```bash
#!/bin/sh

export http_proxy=http://httpproxyserver.something.com:3128
export https_proxy=http://httpsproxyserver.something.com:3128
export no_proxy=localhost,your.domain.com,her.domain.net,his.domain.org,10.0.1.0,10.10.1.10,somethingelse.com
```

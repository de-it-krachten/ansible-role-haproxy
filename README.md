[![CI](https://github.com/de-it-krachten/ansible-role-haproxy/workflows/CI/badge.svg?event=push)](https://github.com/de-it-krachten/ansible-role-haproxy/actions?query=workflow%3ACI)


# ansible-role-haproxy

Manage HAProxy


Platforms
--------------

Supported platforms

- Red Hat Enterprise Linux 7<sup>1</sup>
- Red Hat Enterprise Linux 8<sup>1</sup>
- RockyLinux 8
- AlmaLinux 8<sup>1</sup>
- Debian 10 (Buster)
- Debian 11 (Bullseye)
- Ubuntu 20.04 LTS
- Ubuntu 22.04 LTS

Note:
<sup>1</sup> : no automated testing is performed on these platforms

Role Variables
--------------
<pre><code>
haproxy_firewall_ports:
  - 80

haproxy_config:
  global:
    log: 127.0.0.1 local0
    chroot: /var/lib/haproxy
    stats: socket /var/lib/haproxy/stats user haproxy group haproxy mode 660 level operator
    pidfile: /var/run/haproxy.pid
    user: haproxy
    group: haproxy
    daemon:
    maxconn: 512
  defaults:
    log: global
    mode: http
    option:
      - httplog
      - dontlognull
      - redispatch
    timeout:
      - queue 1m
      - connect 10s
      - client 1m
      - server 1m
      - check 10s
</pre></code>


Example Playbook
----------------

<pre><code>
- name: sample playbook for role 'haproxy'
  hosts: nginx
  roles:
    - nginx
  vars:
    nginx_default_server: true
  tasks:
    - name: Open firewall ports
      include_role:
        name: firewall
      vars:
        firewall_ports:
          - { port: 80, proto: tcp }

    - name: Create index.html for node1
      copy:
        content: node1
        dest: /usr/share/nginx/html/index.html
        mode: "0644"
      when: inventory_hostname == groups['nginx'][0]

    - name: Create index.html for node2
      copy:
        content: node2
        dest: /usr/share/nginx/html/index.html
        mode: "0644"
      when: inventory_hostname == groups['nginx'][1]


- name: sample playbook for role 'haproxy'
  hosts: haproxy
  vars:
    haproxy_firewall_ports:
      - { port: 80, proto: tcp }
      - { port: 8404, proto: tcp }
    haproxy_frontends:
      - name: stats
        options:
          bind: '*:8404'
          mode: http
          stats:
            - enable
            - uri /stats
            - refresh 10s
            - admin if LOCALHOST
      - name: frontend1
        options:
          description: frontend1
          mode: http
          bind: 172.17.0.100:80
          default_backend: backend1
          log: global
      - name: frontend2
        options:
          description: frontend2
          mode: http
          bind: 172.17.0.200:80
          default_backend: backend2
          log: global
    haproxy_backends:
      - name: backend1
        options:
          balance: roundrobin
          option: httpchk HEAD /
          mode: http
          server:
            - node1 {{ hostvars[nginx_node1]['ansible_default_ipv4']['address'] }}:80
            - node2 {{ hostvars[nginx_node2]['ansible_default_ipv4']['address'] }}:80
          source: 172.17.0.100
          log: global
      - name: backend2
        options:
          balance: roundrobin
          option: httpchk HEAD /
          mode: http
          server:
            - node1 {{ hostvars[nginx_node1]['ansible_default_ipv4']['address'] }}:80
            - node2 {{ hostvars[nginx_node2]['ansible_default_ipv4']['address'] }}:80
          source: 172.17.0.200
          log: global
    keepalived_vrrp_sync_groups:
      - name: SG1
        members:
          - vrrp1
          - vrrp2
    keepalived_vrrp_instances:
      - name: vrrp1
        options:
          interface: eth0
          virtual_router_id: 1
          state: "{{ 'MASTER' if keepalived_role == 'master' else 'BACKUP' }}"
          priority: "{{ 150 if keepalived_role == 'master' else 100 }}"
          advert_int: 1
          version: 2
        cluster_ip: 172.17.0.100
        authentication: true
        auth_type: PASS
        auth_pass: TEST1
      - name: vrrp2
        options:
          interface: eth0
          virtual_router_id: 2
          state: "{{ 'MASTER' if keepalived_role == 'master' else 'BACKUP' }}"
          priority: "{{ 150 if keepalived_role == 'master' else 100 }}"
          advert_int: 1
          version: 2
        cluster_ip: 172.17.0.200
        authentication: true
        auth_type: AH
        auth_pass: TEST2
  roles:
    - keepalived
  tasks:
    - name: Save nginx nodes
      set_fact:
        nginx_node1: "{{ groups['nginx'][0] }}"
        nginx_node2: "{{ groups['nginx'][1] }}"

    - name: Include role 'haproxy'
      include_role:
        name: haproxy
</pre></code>

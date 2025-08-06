[![CI](https://github.com/de-it-krachten/ansible-role-haproxy/workflows/CI/badge.svg?event=push)](https://github.com/de-it-krachten/ansible-role-haproxy/actions?query=workflow%3ACI)


# ansible-role-haproxy

Manage HAProxy



## Dependencies

#### Roles
None

#### Collections
- ansible.posix

## Platforms

Supported platforms

- Red Hat Enterprise Linux 8<sup>1</sup>
- Red Hat Enterprise Linux 9<sup>1</sup>
- Red Hat Enterprise Linux 10<sup>1</sup>
- RockyLinux 8
- RockyLinux 9
- RockyLinux 10
- OracleLinux 8
- OracleLinux 9
- OracleLinux 10
- AlmaLinux 8
- AlmaLinux 9
- AlmaLinux 10
- SUSE Linux Enterprise 15<sup>1</sup>
- Debian 11 (Bullseye)
- Debian 12 (Bookworm)
- Debian 13 (Trixie)
- Ubuntu 20.04 LTS
- Ubuntu 22.04 LTS
- Ubuntu 24.04 LTS

Note:
<sup>1</sup> : no automated testing is performed on these platforms

## Role Variables
### defaults/main.yml
<pre><code>
# HAProxy configuration file
haproxy_config_file: /etc/haproxy/haproxy.cfg

# Template to use for haproxy.cfg
haproxy_template: templates/haproxy.cfg.j2

# Ports to open on the firewall
haproxy_firewall_ports: []

# Should firewall ports be managed by this role
haproxy_manage_firewall: true

# Create backup of existing configuration
haproxy_config_backup: false

# Chroot path
haproxy_chroot_path: /var/lib/haproxy

# Default proxy configuration
haproxy_config:
  global:
    log: 127.0.0.1 local0
    chroot: "{{ haproxy_chroot_path }}"
    stats: socket {{ haproxy_chroot_path }}/stats user haproxy group haproxy mode 660 level operator
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

# Logging via rsyslog
haproxy_syslog_template: rsyslog.conf.j2
haproxy_syslog_file: /etc/rsyslog.d/10-haproxy.conf
haproxy_syslog_method: udp  # can be 'socket'
haproxy_facility: local0
haproxy_log_file: /var/log/haproxy.log
</pre></code>

### defaults/family-Debian.yml
<pre><code>
# List of packages required for haproxy
haproxy_packages:
  - haproxy
</pre></code>

### defaults/family-RedHat.yml
<pre><code>
# List of packages required for haproxy
haproxy_packages:
  - haproxy
</pre></code>

### defaults/family-Suse.yml
<pre><code>
# List of packages required for haproxy
haproxy_packages:
  - haproxy
</pre></code>




## Example Playbook
### molecule/default/converge.yml
<pre><code>
- name: sample playbook for role 'haproxy'
  hosts: nginx
  roles:
    - deitkrachten.rsyslog
    - deitkrachten.nginx
  vars:
    nginx_default_server: true
  tasks:
    - name: Open firewall ports
      include_role:
        name: deitkrachten.firewall
      vars:
        firewall_ports:
          - port: 80
            proto: tcp
    - name: Create index.html for node1
      copy:
        content: node1
        dest: /usr/share/nginx/html/index.html
        mode: '0644'
      when: inventory_hostname == groups['nginx'][0]
    - name: Create index.html for node2
      copy:
        content: node2
        dest: /usr/share/nginx/html/index.html
        mode: '0644'
      when: inventory_hostname == groups['nginx'][1]
- name: sample playbook for role 'haproxy'
  hosts: haproxy
  vars:
    haproxy_firewall_ports:
      - port: 80
        proto: tcp
      - port: 8404
        proto: tcp
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
          state: '{{ ''MASTER'' if keepalived_role == ''master'' else ''BACKUP'' }}'
          priority: '{{ 150 if keepalived_role == ''master'' else 100 }}'
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
          state: '{{ ''MASTER'' if keepalived_role == ''master'' else ''BACKUP'' }}'
          priority: '{{ 150 if keepalived_role == ''master'' else 100 }}'
          advert_int: 1
          version: 2
        cluster_ip: 172.17.0.200
        authentication: true
        auth_type: AH
        auth_pass: TEST2
  roles:
    - deitkrachten.rsyslog
    - deitkrachten.keepalived
  tasks:
    - name: Save nginx nodes
      set_fact:
        nginx_node1: '{{ groups[''nginx''][0] }}'
        nginx_node2: '{{ groups[''nginx''][1] }}'
    - name: Include role 'haproxy'
      include_role:
        name: haproxy
</pre></code>

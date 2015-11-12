# Ansible Role: Shorewall

This role will assume the setup of [shorewall](http://shorewall.net/). 

## Requirements

- Ansible 1.9.2+
- Debian-based linux-distribution

## Dependencies

None.

## Installation

git clone https://github.com/whotwagner/ansible-role-shorewall.git

### Configuration example


#### Shorewall configuration with default templates

```
    shorewall_startup: 1
    shorewall_ipforward: "On"
    shorewall_configs_dir: /etc/shorewall

    shorewall_configs:
      zones:
        - { name: inet, 
            type: ipv4,
            interface: { name: $INETIF, broadcast: detect, options: "blacklist,routeback,nosmurfs" }
          }
        - { 
            name: lan, 
            type: ipv4,
            interface: { name: $LANIF, broadcast: detect, options: "blacklist,routeback,bridge,nosmurfs" }
          }
      policy:
        - { source: fw,    dest: all,   policy: ACCEPT }
        - { source: lan,   dest: inet,  policy: ACCEPT }
        - { source: inet,  dest: inet,  policy: DROP }
        - { source: inet,  dest: fw,    policy: DROP }
        - { source: inet,  dest: lan,   policy: DROP }
        - THIS POLICY HAS TO BE THE LAST
        - { source: all,   dest: all,   policy: REJECT, log: info } 
      rules:
        - { action: DNAT, source: inet, dest: "lan:192.168.213.10:22", proto: tcp, dest_port: 10022 }
        - Permit access to SSH
        - { action: SSH/ACCEPT,  source: inet, dest: fw }
        - { action: ACCEPT,      source: inet, dest: fw, proto: tcp, dest_port: "443,8006" }
        - PING Rules
        - { action: Ping/ACCEPT, source: all, dest: all }
      masq:
        - { interface: $INETIF, source: 192.168.213.0/24 }
      params:
        - Interfaces
        - { name: INETIF, value: eth0 }
        - { name: LANIF, value: br0 }

```

## Example playbook

    - hosts: servers
      roles:
         - { role: shorewall }

# Licence

MIT

# Author information

TOSCOM [**(http://www.toscom.at/)**](http://www.toscom.at)

Author of the forked Project: ELAO [**(http://www.elao.com/)**](http://www.elao.com)

# Ansible role Proxmox

## Install

```shell
ansible-galaxy role install git+https://github.com/sergelogvinov/ansible-role-proxmox.git,main
```

## Install Proxmox

```yaml
# proxmox.yml

- hosts: all
  vars:
    proxmox_ipset:
      - name: controlplane
        entries:
          - 1.2.3.4
          - 1.2.3.5
      - name: admin
        entries:
          - 1.1.1.1

    proxmox_groups:
      - name: admin
        rules:
          - IN SSH(ACCEPT) -source +admin -log nolog
          - IN ACCEPT -source +admin -p tcp -dport 8006 -log nolog
      - name: controlplane
        rules:
          - IN ACCEPT -source +controlplane -p tcp -dport 8006 -log nolog

    proxmox_rules:
      - IN DNS(ACCEPT) -i vmbr0 -log nolog
      - IN ACCEPT -i vmbr0 -p udp -dport 67 -sport 68 -log nolog
      - IN ACCEPT -p ipv6-icmp -log nolog
      - IN Ping(ACCEPT) -log nolog
      - GROUP admin
      - GROUP controlplane
    proxmox_node_rules:
      - IN NeighborDiscovery(ACCEPT) -i {{ ansible_default_ipv6.interface }} -log nolog

  roles:
    - ansible-role-proxmox
```

```ini
# proxmox.ini

[all]
proxmox   ansible_host=1.2.3.4 ansible_ssh_user=root
```

Install proxmox to the server

```shell
ansible-playbook -Dv -i proxmox.ini proxmox.yml
```

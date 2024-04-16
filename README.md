# CrossFi Validator Ansible Role

This role provides an easy way to install a CrossFi validator node (supervised using cosmovisor) onto a Linux machine. 

## Variables 

| Variable | Description | Default |
|----------|-------------|---------|
| `crossfi_moniker` | The moniker (name) for the validator | Hostname from inventory |
| `crossfi_initial_version` | The version of crossfid that will be used when cosmovisor first starts. Accepts any version number from a Github release. | `0.2.0-prebuild6` |
| `crossfi_user` | Username for the user running crossfid | `crossfi` |
| `crossfi_home` | Home directory where CrossFi configuration and data is stored | `/var/lib/crossfi` |
| `crossfi_cosmovisor_version` | The version of cosmovisor to use. Accepts any version number from a Github release. | `v1.5.0` |
| `crossfi_cosmovisor_allow_download_binaries` | Whether cosmovisor will automatically download new binaries | `true` |
| `crossfi_cosmovisor_restart_after_update` | Whether cosmovisor should restart crossfid after downloading an update. While this is `true` by default to facilitate the initial sync, you should consider setting it to `false` once your validator is fully operational. | `true` |
| `crossfi_prometheus_retention_time` | Setting this to a value greater than 0 enables the prometheus exporter. | `120` |
| `crossfi_firewall_rules_enable` | This can be set to `ufw` or `firewalld` to automatically allow traffic the peer-to-peer port on the machine | (disabled) |

## Usage

Create a `requirements.yml` file declaring a dependency on this role:

```yaml
roles:
- name: crossfi
  src: https://github.com/cardinate/crossfi-role
  version: main
```

Install the role using ansible-galaxy:

```sh
ansible-galaxy role install -r requirements.yml
```

Create an inventory file (e.g. `inventory.ini`) describing the node(s) you want to deploy:

```ini
[nodes]
my-node

[my-node:vars]
crossfi_moniker=my-validator-1
```

Create a `site.yml` file that describes how to deploy the nodes:

```yaml
- hosts: nodes
  roles:
    - role: crossfi
```

Execute the playbook:

```sh
ansible-playbook -i inventory.ini site.yml
```

## Limitations

This playbook currently only supports the rather slow ‘blocksync’ method. If you want
to sync faster, adjust `crossfi_initial_version` to a higher version number and use one of
the methods described in the [sync options](https://docs.crossfi.org/crossfi-chain/node-operators/join-a-network#sync-options) documentation.


## Tested With

* Ansible 2.16.5
* Ubuntu 22.04

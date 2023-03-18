# ansible-nocloud-metadata-server

Installs and configures an instance of the
[NoCloud Metadata Server](https://github.com/jalseth/nocloud-metadata-server).

## Installation

```sh
ansible-galaxy install jalseth.nocloud_metadata_server
```

### Python dependencies

- `jmespath` for JSON parsing


```sh
pip install --user jmespath
```

## Usage

You may wish to configure a server with multiple instances of the NoCloud
Metadata server, such as separating instances per VLAN that serve different
server types. The example below demonstrates such a configuration.

```yaml
name: Configure NoCloud Metadata Server
hosts: your.host.name
become: true
tasks:
- name: Install NMS server binary
  include_role:
    name: jalseth.nocloud_metadata_server
  vars:
    nocloud_metadata_server_enable_systemd: false
    nocloud_metadata_server_config: {}

- name: Configure NMS instances
  include_role:
    name: jalseth.nocloud_metadata_server
  vars:
    nocloud_metadata_server_systemd_service_name: "nocloud-metadata-server-{{ nms.name }}"
    nocloud_metadata_server_systemd_service_file_path: "/lib/systemd/system/nocloud-metadata-server-{{ nms.name}}.service"
    nocloud_metadata_server_config_file_path: "/etc/nocloud-metadata-server-{{ nms.name }}.yaml"
    nocloud_metadata_server_config: "{{ nms.config }}"
  with_items:
  - name: vlan123
    config:
      listenAddress: "10.10.123.2"
      matchPatterns: ["123"]
      instanceConfig:
        hostname: onetwothree
        enableInstanceIDSuffix: true
        enableHostnameSuffix: true
  - name: vlan234
    config:
      listenAddress: "10.10.234.2"
      matchPatterns: ["234"]
      instanceConfig:
        hostname: twothreefour
        enableInstanceIDSuffix: true
        enableHostnameSuffix: true
  loop_control:
    loop_var: nms
```

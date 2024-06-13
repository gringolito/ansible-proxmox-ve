# ansible-proxmox-ve
Ansible role for configuring a Proxmox VE server/cluster.

## Requirements

This role assumes that you are running it on Proxmox server installation.

### Cluster joining

For nodes to join on a existing cluster, the current node and existing member should have SSH
connection with SSH keys setup.

## Role Variables

### General configurations

| Variable | Comment |
| -------- | ------- |
| **pve_force_reboot**<br>*boolean* | <p>Force the server to reboot after executing the role.<p>**Choices:**<br>- `true`<br>- `false` &larr; (default) |
| **pve_force_reboot**<br>*boolean* | <p>Force the server to reboot after executing the role.<p>**Choices:**<br>- `true`<br>- `false` &larr; (default) |
| **pve_enterprise**<br>*boolean* | <p>Defines whether the server has an PVE-Enterprise license.<p>**Choices:**<br>- `true`<br>- `false` &larr; (default) |
| **pve_datacenter_tag_style_order**<br>*string* | <p>Defines the order of the guests tags. Set to `config` to respect the configuration order. Alphabetical order will be used otherwise. |

### PCI passthrough (IOMMU) configurations

| Variable | Comment |
| -------- | ------- |
| **pve_iommu_passthrough**<br>*boolean* | <p>Enable/disable PCI passthrough configuration.<p>**Choices:**<br>- `true`<br>- `false` &larr; (default) |
| **pve_iommu_intel**<br>*boolean* | <p>Enable/disable the IOMMU for Intel processors with VT-d/VT-x.<p>**Choices:**<br>- `true`<br>- `false` &larr; (default) |

### PVE users/groups configurations

| Variable | Comment |
| -------- | ------- |
| **pve_admin_group**<br>*string* | <p>Define the Administrators group on the PVE realm.<p>**Default:** `Administrators` |
| **pve_admin_user**<br>*string* | <p>Define the administrator user on the PVE realm.<p>**Default:** `admin@pve` |
| **pve_audit_group**<br>*string* | <p>Define the Auditors group on the PVE realm.<p>**Default:** `Auditors` |
| **pve_audit_user**<br>*string* | <p>Define the audit user on the PVE realm.<p>**Default:** `audit@pve` |

### PVE cluster configurations

| Variable | Comment |
| -------- | ------- |
| **pve_cluster_create**<br>*string* | <p>Define the name of PVE cluster to be created. This variable must be defined in only one node per cluster. |
| **pve_cluster_join_address**<br>*string* | <p>Define the address of an existing cluster member to join.<p>**Format:** DNS-like name, IP address or IPv6 address. |
| **pve_cluster_link**<br>*string* | <p>Force the cluster communication to go through the specified link address.<p>**Format:** DNS-like name, IP address or IPv6 address. |
| **pve_cluster_ha_shutdown_policy**<br>*string* | <p>Define the shutdown policy for HA-enabled guests.<p>Please refer to the **Shutdown Policy** section on the official Proxmox [High Availability](https://pve.proxmox.com/wiki/High_Availability#ha_manager_node_maintenance) guide for more information.<p>**Choices:**<br>- `freeze`<br>- `failover`<br>- `migrate`<br>- `conditional` |
| **pve_cluster_migration_network**<br>*string* | <p>Set the network for running guests migration.<p>**Format:** IP address/mask or IPv6 address/mask. |

### PVE local network configurations

| Variable | Comment |
| -------- | ------- |
| **pve_network_interfaces**<br>*list / elements=string or [interface_config](#interface_config-attributes)* | <p>Defines the local network interfaces configuration. |
| **pve_network_bridges**<br>*list / elements=[bridge_config](#bridge_config-attributes)* | <p>Defines the local network bridges configuration. |
| **pve_network_interface_alias**<br>*list / elements=[interface_alias](#interface_alias-attributes)* | <p>Defines persistent interface alias for local network interfaces. Use this to rename interface to a more convenient names. |

#### interface_config Attributes

| Attribute | Comment | Required / Optional |
| --------- | ------- | ------------------- |
| **name**<br>*string* | <p>The interface name.<p>For VLAN tagged interfaces append the VLAN tag on the interface name (e.g: `eno1.10`).<p>Informing only the `name` attribute is the same as informing a plain `string`. | Required |
| **allow**<br>*string* | <p>Identify the subsystem that should brought up the interface.<p>**Choices:**<br>- `hotplug` | Optional |
| **address**<br>*string* | <p>The address to be assigned to the interface.<p>**Format:** IP address/mask. | Optional |
| **gateway**<br>*string* | <p>The network gateway address.<p>**Format:** IP address. | Optional |
| **description**<br>*string* | <p>Free-form comment to be appended on the interface configuration. | Optional |

#### bridge_config Attributes

| Attribute | Comment | Required / Optional |
| --------- | ------- | ------------------- |
| **name**<br>*string* | <p>The bridge name. | Required |
| **interfaces**<br>*string*<br>*list / elements=string* | <p>The physical interface(s) that should be bridged into. | Required |
| **address**<br>*string* | <p>The address to be assigned to the brigde.<p>**Format:** IP address/mask. | Optional |
| **gateway**<br>*string* | <p>The network gateway address.<p>**Format:** IP address. | Optional |
| **vlans**<br>*string* | <p>Turn the bridge into a VLAN-aware bridge for the configured VLANs.<p>**Format:** comma separated list of VLAN ID, range of VLAN IDs can be grouped with dashes (e.g. `2-1000,2000`). | Optional |
| **description**<br>*string* | <p>Free-form comment to be appended on the interface configuration. | Optional |

#### interface_alias Attributes

| Attribute | Comment | Required / Optional |
| --------- | ------- | ------------------- |
| **mac_address**<br>*string* | <p>The MAC address to match the interface.<p>At least one in `mac_address` or `driver` MUST be defined. | Optional |
| **driver**<br>*string* | <p>The driver to match the interface.<p>To discover your interface driver you can run:<br>`# ethtool -i {interface_name}`<p>At least one in `mac_address` or `driver` MUST be defined. | Optional |
| **interface_name**<br>*string* | <p>The name (*alias*) to be configured for the interface. | Required |

### PVE storage configurations

| Variable | Comment |
| -------- | ------- |
| **pve_cifs_shares**<br>*list / elements=[cifs_share](#cifs_share-attributes)* | <p>Define the CIFS/SMB shares to be added on the server/cluster storage. |
| **pve_nfs_mounts**<br>*list / elements=[nfs_mount](#nfs_mount-attributes)* | <p>Define the NFS mounts to be added on the server/cluster storage. |
| **pve_iscsi_targets**<br>*list / elements=[iscsi_target](#iscsi_target-attributes)* | <p>Define the iSCSI targets to be added on the server/cluster storage. |
| **pve_iscsi_authentication**<br>*[iscsi_authentication](#iscsi_authentication-attributes)* | <p>Define the iSCSI authentication for the iSCSI targets, whenever authentication is needed. |
| **pve_zfs_pools**<br>*list / elements=[zfs_pool](#zfs_pool-attributes)* | <p>Defined the ZFS pools to be added on the server/cluster storage. |
| **pve_lvm_mounts**<br>*list / elements=[lvm_mount](#lvm_mount-attributes)* | <p>Define the LVM mounts to be added on the server/cluster storage. |

#### cifs_share Attributes

| Attribute | Comment | Required / Optional |
| --------- | ------- | ------------------- |
| **name**<br>*string* | <p>The storage name on the Proxmox server/cluster. | Required |
| **server**<br>*string* | <p>The CIFS/SMB server address.<p>**Format:** DNS-like name, IP address or IPv6 address. | Required |
| **share**<br>*string* | <p>The CIFS/SMB share name. | Required |
| **username**<br>*string* | <p>The CIFS/SMB username credentials. | Required |
| **password**<br>*string* | <p>The CIFS/SMB password credentials. | Required |
| **content**<br>*string* | <p>Comma separated list of the storage content types.<p>Please refer to the **Common Storage Properties** section on the official Proxmox [Storage](https://pve.proxmox.com/pve-docs/chapter-pvesm.html#_common_storage_properties) documentation for more information.<p>**Choices:**<br>- `images` &larr; (default)<br>- `rootdir`<br>- `vztmpl`<br>- `backup`<br>- `iso`<br>- `snippets` | Optional |
| **shared**<br>*boolean* | <p>Indicate that this is a single storage with the same contents on all nodes.<p>**Choices:**<br>- `true` &larr; (default)<br>- `false` | Optional |

#### nfs_mount Attributes

| Attribute | Comment | Required / Optional |
| --------- | ------- | ------------------- |
| **name**<br>*string* | <p>The storage name on the Proxmox server/cluster. | Required |
| **server**<br>*string* | <p>The NFS server address.<p>**Format:** DNS-like name, IP address or IPv6 address. | Required |
| **export**<br>*path* | <p>The NFS export path. | Required |
| **content**<br>*string* | <p>Comma separated list of the storage content types.<p>Please refer to the **Common Storage Properties** section on the official Proxmox [Storage](https://pve.proxmox.com/pve-docs/chapter-pvesm.html#_common_storage_properties) documentation for more information.<p>**Choices:**<br>- `images` &larr; (default)<br>- `rootdir`<br>- `vztmpl`<br>- `backup`<br>- `iso`<br>- `snippets` | Optional |
| **nodes**<br>*string* | <p>Comma separated list of nodes where the should be available, set `all` for all nodes on the cluster.<p>**Default:** `all` | Optional |

#### iscsi_target Attributes

| Attribute | Comment | Required / Optional |
| --------- | ------- | ------------------- |
| **name**<br>*string* | <p>The storage name on the Proxmox server/cluster. | Required |
| **portal**<br>*string* | <p>The iSCSI server address.<p>**Format:** DNS-like name, IP address or IPv6 address. | Required |
| **target**<br>*path* | <p>The iSCSI target IQN.<p>**Format:** base-name:target| Required |
| **content**<br>*string* | <p>The iSCSI storage content types.<p>If you want to use LVM on top of iSCSI, it make sense to set content `none`. That way it is not possible to create VMs using iSCSI LUNs directly.<p>Please refer to the **Common Storage Properties** section on the official Proxmox [Storage](https://pve.proxmox.com/pve-docs/chapter-pvesm.html#_common_storage_properties) documentation for more information.<p>**Choices:**<br>- `images`<br>- `none` &larr; (default) | Optional |
| **nodes**<br>*string* | <p>Comma separated list of nodes where the should be available, set `all` for all nodes on the cluster.<p>**Default:** `all` | Optional |

#### iscsi_authentication Attributes

| Attribute | Comment | Required / Optional |
| --------- | ------- | ------------------- |
| **username**<br>*string* | <p>The iSCSI User credentials. | Required |
| **password**<br>*string* | <p>The iSCSI User secret credentials. | Required |
| **username_in**<br>*string* | <p>The iSCSI Peer (*initiator*) User credentials. | Required |
| **password_in**<br>*string* | <p>The iSCSI Peer (*initiator*) secret credentials. | Required |

#### zfs_pool Attributes

| Attribute | Comment | Required / Optional |
| --------- | ------- | ------------------- |
| **name**<br>*string* | <p>The storage name on the Proxmox server/cluster. | Required |
| **pool**<br>*string* | <p>The ZFS pool name. | Required |
| **content**<br>*string* | <p>Comma separated list of the storage content types.<p>Please refer to the **Common Storage Properties** section on the official Proxmox [Storage](https://pve.proxmox.com/pve-docs/chapter-pvesm.html#_common_storage_properties) documentation for more information.<p>**Choices:**<br>- `images` &larr; (default)<br>- `rootdir`<br> | Optional |

#### lvm_mount Attributes

| Attribute | Comment | Required / Optional |
| --------- | ------- | ------------------- |
| **name**<br>*string* | <p>The storage name on the Proxmox server/cluster. | Required |
| **vgname**<br>*string* | <p>LVM volume group name. This must point to an existing volume group. | Required |
| **content**<br>*string* | <p>Comma separated list of the storage content types.<p>Please refer to the **Common Storage Properties** section on the official Proxmox [Storage](https://pve.proxmox.com/pve-docs/chapter-pvesm.html#_common_storage_properties) documentation for more information.<p>**Choices:**<br>- `images` &larr; (default)<br>- `rootdir`<br> | Optional |

## Example Playbook

```yaml
  - name: Configuring Proxmox VE server
    hosts: all
    roles:
      - gringolito.ansible_proxmox_ve
    vars:
      pve_datacenter_tag_style_order: config
      pve_iommu_intel: true
      pve_iommu_passthrough: true
      pve_network_interface_alias:
        - driver: r8152
          interface_name: enusb0
      pve_nfs_mounts:
        - name: my-nfs
          server: nfs.gringolito.com
          export: /mnt/nfs/pve
          content: images,backup,iso
```

## License

Beerware

## Author Information

Filipe Utzig < filipe at gringolito.com >

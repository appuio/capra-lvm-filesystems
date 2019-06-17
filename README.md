# capra-lvm-filesystems

Ansible role for managing logical volumes on LVM, ensure they have a filesystem
and are mounted.

## Variables

* `capra_lvm_filesystem_specs`: List of hashes describing the volumes.
  * `name`: Name of the volume.
  * `vg`: Name of the volume group on which to create the volume.
    Defaults to `capra_lvm_filesystem_vg`.
  * `size`: Size of the volume in bytes. Usage of human readable exponents like
    M, G, etc. is possible (e.g 42G).
  * `fstype`: Type of file system to create on the volume.
    Defaults to `capra_lvm_filesystem_fstype`.
  * `path`: Path where the filesystem will be mounted to.
    Defaults to `{{capra_lvm_filesystem_parent_path }}/{{ name }}`.
  * `owner`: File owner owner of the mount.
    Defaults to `capra_lvm_filesystem_owner`.
  * `group`: File group of the mount.
    Defaults to `capra_lvm_filesystem_group`.
  * `mode`: File permissions of the mound.
    Defaults to `capra_lvm_filesystem_mode`.
  * `seuser`: SELinux user of the mount.
    Defaults to `capra_lvm_filesystem_seuser`.
  * `serole`: SELinux role of the mount.
    Defaults to `capra_lvm_filesystem_serole`.
  * `setype`: SELinux type of the mount.
    Defaults to `capra_lvm_filesystem_setype`.
  * `selevel`: SELinux level of the mount.
    Defaults to `capra_lvm_filesystem_setype`.
  * `options`: Options applied to the mount.
    Defaults to `capra_lvm_filesystem_options`.
  * `state`: Whether the volume is supposed or be `present` or `absent`.
    Defaults to `present`.
* `capra_lvm_filesystem_vg`: Default volume group to place volumes in.
  Defaults to `vg`.
* `capra_lvm_filesystem_fstype`: Default file system type placed on the logical
  volume.
  Defaults to `xfs`.
* `capra_lvm_filesystem_parent_path`: Default path where the volumes will be
  mounted to.  The name of the volume will be appended.
  Defaults to `/mnt`.
* `capra_lvm_filesystem_owner`: Default mount point file owner.
  Defaults to `root`.
* `capra_lvm_filesystem_group`: Default mount point file group.
  Defaults to `root`.
* `capra_lvm_filesystem_mode`: Default mount point file access mode.
  Defaults to `0755`.
* `capra_lvm_filesystem_seuser`: Default mount point SELinux user.
  Defaults to `unconfined_u`.
* `capra_lvm_filesystem_serole`: Default mount point SELinux role.
  Defaults to `object_r`.
* `capra_lvm_filesystem_setype`: Default mount point SELinux type.
  Defaults to `svirt_sandbox_file_t`.
* `capra_lvm_filesystem_selevel`: Default mount point SELinux level.
  Defaults to `s0`.
* `capra_lvm_filesystem_options`: String of default mount point options as it
  wold be set in `/etc/fstab`.
  Defaults to `defaults`.

## Tags

The following tags can be used to control the executed actions:

* `capra-lvm-filesystem-prerequisites`:
  Actions required to be done once which ensure the other actions can succeed.
* `capra-lvm-filesystem-create`:
  Actions required to create a new volume/filesystem/mount.
* `capra-lvm-filesystem-remove`:
  Actions required to remove a mount/volume.

## Example Playbook

```yaml
- name: Configure local storage
  hosts: all
  vars:
    my_local_volume_path: "/var/lib/rancher/local-volume"
    capra_lvm_filesystem_fstype: ext4
    capra_lvm_filesystem_filesystem_options: ~
  roles:
    - role: "capra-lvm-filesystems"
      capra_lvm_filesystem_mount_parent: "{{ my_local_volume_path }}/ssd"
      capra_lvm_filesystem_specs:
        - name: "{{ inventory_hostname + '7jOa' | to_uuid }}"
          size: 5G
        - name: "{{ inventory_hostname + 'NobF' | to_uuid }}"
          size: 5G
    - role: "capra-lvm-filesystems"
      capra_lvm_filesystem_mount_parent: "{{ my_local_volume_path }}/ssd-retain"
      capra_lvm_filesystem_specs:
        - name: "{{ inventory_hostname + 'eX5e' | to_uuid }}"
          size: 5G
```

The playbook will create three 5G volumes on each host. Two will be mounted to
`/var/lib/rancher/ssd/<name>` and one to `/var/lib/rancher/ssd-retain/<name>`.
Each volume will have a unique name.


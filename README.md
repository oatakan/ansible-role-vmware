Role Name
=========

An Ansible role for provisioning and deprovisioning virtual machines on VMware vSphere.

Requirements
------------

- Access to a VMware vCenter instance.
- The VMware modules used by this role, including `community.vmware.vmware_guest_boot_manager`, must be available to Ansible.
- Environment variables `VMWARE_HOST`, `VMWARE_USER`, and `VMWARE_PASSWORD` can be used to override the configured provider credentials.

Role Variables
--------------

Common variables from `defaults/main.yml`:

- `role_action`: `provision` or `deprovision`
- `ansible_port`: default SSH/WinRM port used by the connection wait task
- `instance_wait_retry_limit`: async retry count for create/power operations
- `instance_wait_connection_timeout`: timeout used when waiting for the guest port
- `ip_wait_retry_limit`: retry count used while polling vSphere for a guest IP address
- `enable_custom_attributes`: attach VMware custom attributes after provisioning
- `enable_tags`: attach VMware tags after provisioning

Per-node variables:

- `name`: virtual machine name
- `template`: template to clone from
- `memory`, `cpu`: hardware sizing
- `networks`, `customization`, `disk`, `datastore`, and other existing `vmware_guest` options supported by this role
- `wait_for_ip_address`: whether to wait for VMware Tools/IP reporting and guest connectivity
- `boot_order`: optional non-empty list of boot devices. When set, the role creates the VM powered off, applies the requested boot order, and only then powers the VM on. When omitted, the role keeps the current behavior and clones the VM directly to `poweredon`.

Dependencies
------------

No role dependencies are declared in `meta/main.yml`.

Example Playbook
----------------

```yaml
- hosts: localhost
  gather_facts: false
  vars:
    role_action: provision
    providers:
      vcenter:
        hostname: vcenter.example.com
        username: administrator@vsphere.local
        password: secret
        datacenter: DC1
        cluster: Cluster1
        folder: /DC1/vm
    nodes:
      - name: web-01
        template: rhel9-template
        memory: 4096
        cpu: 2
        wait_for_ip_address: true
      - name: rescue-01
        template: rhel9-template
        memory: 4096
        cpu: 2
        boot_order:
          - disk
          - ethernet
  roles:
    - ansible-role-vmware
```

License
-------

BSD

Author Information
------------------

An optional section for the role authors to include contact information, or a website (HTML is not allowed).

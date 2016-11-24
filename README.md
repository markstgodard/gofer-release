# 🏃 gofer-release 🏃
[Bosh](https://bosh.io) release for [Gofer](http://www.dictionary.com/browse/gofer). Gofer is kind of like [Kuryr](https://github.com/openstack/kuryr) for [Cloud Foundry](https://github.com/cloudfoundry/cf-release) [container networking](https://github.com/cloudfoundry-incubator/netman-release)

Kuryr means *messenger*, Gofer means *"go for"*

This release is a prototype for CF container networking using:
- [CNI](https://github.com/containernetworking/cni) plugin:
  - [Neutron](https://github.com/openstack/neutron) API for control plane (create networks,subnets,ports)
  - [Keystone](https://github.com/openstack/keystone) API for authentication
  - uses a delegate openvswitch (OVS) CNI plugin for VIF binding
- tested using a local [devstack](http://docs.openstack.org/developer/devstack/) for Neutron, Keystone

## Config
Update bosh manifest to include `release` and `template` in diego cell job:
```yaml
releases:
- name: diego
  version: latest
- name: cflinuxfs2-rootfs
  version: latest
- name: netman
  version: latest
- name: garden-runc
  version: latest
- name: cf
  version: latest
- name: gofer
  version: latest
```
```yaml
- instances: 1
  name: cell_z1
  templates:
  - name: rep
    release: diego
  - name: garden
    release: garden-runc
  - name: metron_agent
    release: cf
  - name: cfdot
    release: diego
  - name: garden-cni
    release: netman
  - name: netmon
    release: netman
    . . .
  - name: openvswitch
    release: gofer
  - name: cni-gofer
    release: gofer
```

```
properties:

  garden:
    network_plugin: /var/vcap/packages/runc-cni/bin/garden-external-networker
    network_plugin_extra_args:
    - --configFile=/var/vcap/jobs/garden-cni/config/adapter.json

  garden-cni:
    cni_config_dir: /var/vcap/jobs/cni-gofer/config/cni
    cni_plugin_dir: /var/vcap/packages/gofer/bin

  cni-gofer:
     neutron_url: http://192.168.56.101:9696
     keystone_url: http://192.168.56.101:5000
     keystone_username: admin
     keystone_password: secret
     state_dir: /var/vcap/data/cni-gofer/containers
     bridge_name: ovs-bridge
```

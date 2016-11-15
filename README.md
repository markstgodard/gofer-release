# gofer-release 🏃
[Bosh](https://bosh.io) release for [Gofer](http://www.dictionary.com/browse/gofer). Gofer is kind of like [Kuryr](https://github.com/openstack/kuryr) for [Cloud Foundry](https://github.com/cloudfoundry/cf-release) [container networking](https://github.com/cloudfoundry-incubator/netman-release)

Kuryr means *messenger*, Gofer means *"go for"*

This release is a prototype for CF container networking using:
- [Neutron](https://github.com/openstack/neutron)-aware [CNI](https://github.com/containernetworking/cni) plugin
- openvswitch (OVS)

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

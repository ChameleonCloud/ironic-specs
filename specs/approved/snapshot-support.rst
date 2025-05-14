..
 This work is licensed under a Creative Commons Attribution 3.0 Unported
 License.

 http://creativecommons.org/licenses/by/3.0/legalcode

=================
Snapshot support
=================

https://bugs.launchpad.net/ironic/+bug/2110694

This spec revives the retired one: https://specs.openstack.org/openstack/ironic-specs/specs/retired/snapshot-support.html
Due to the introduction of service-steps to ironic, the problem now appears
more tractible.

Problem description
===================

Snapshot is not a new thing, it was available for virtual machines for a long
time. Snapshot is useful for instance backup, image reusing, etc, but there is
no such support for bare metal as its more complex than passing the request to
libvirt.

Bare metal snapshot may not match virtual machine's in speed and efficiency, but
it could address following requirements:

As an operator, I want to be able to back up a bare metal instance periodically
and when there is hardware failure, the same image can be applied to another
machine.

As an operator, I want to be able to build a master image from a post customized
instance, capture the system into an image and apply to other similar machines.


Proposed change
===============

The proposal is to use service-steps to implement a process similar to rescue
and deployment, where a node with an active instance is rebooted into a ramdisk,
ironic instructs IPA to capture the contents of the node's disks to remote
storage (as either block storage volume or glance image), and finally reboots
back into the active instance.

The initial proposal is to target only the root disk of the node, and to capture
the entire disk rather than only select partitions. For simplicity, the entire
disk can be treated as a raw disk image without modification.

It should be possible to capture the disk contents in a streaming fashion,
piping through qemu-img to add metadata, and ultimately uploading as glance
image data.

# meta-vmware

meta-vmware provides an [Open Embedded](http://www.openembedded.org)
board-support layer to generate [VMware](http://www.vmware.com) guest
images. It is built on top of [CAROS](http://www.caros.io/) and depends
on linux-caros from
[meta-caros](https://github.com/carosio/meta-caros), however, it can
easily be modified to work with linux-yocto.

This README file contains information on building with the meta-vmware
BSP layer. Please see the corresponding sections below for details.


## Patches

Please submit any patches against this BSP to Travelping (info@travelping.com)


## Table of Contents

I. Building the meta-vmware BSP layer
II. OVF Profiling
III. Changing Netowrk Interface Types

### I. Building the meta-vmware SP layer

In order to build an image with BSP support for a given release, you
need to clone the poky repository.

Having done that, and assuming you placed the folder
at the top-level of your yocto build tree, you can build a
vmware image by adding the location of the meta-vmware
layer to bblayers.conf, along with any other layers needed (to access
common metadata shared between BSPs) e.g.:
```
  yocto/meta-xxxx \
  yocto/meta-xxxx/meta-vmware \
```

To enable the vmware layer, add the vmware MACHINE to local.conf:
```
MACHINE ?= "vmware"
```

You should then be able to build a vmware image as such:
```shell
  $ source oe-init-build-env
  $ bitbake core-image-minimal
```

At the end of a successful build, you should have an
virtual machine disk(vmdk) you can boot that as
hard disk from an virtual maschine.

### II. OVF Profiling

This release adds OVF profiling support to build vAPPs.

To add OVF support to a new image add the following line to your image recipe:
```
  require recipes-vmware/ovf/vmware-ovf.inc
```

The OVF task checks if the target MACHINE type is "vmware", so no
unneeded functionality is added, if the image is built for a different target.

Use the following parameters for customization in local.conf

```
  * CORE_NUMBER       - # of Core(s) per VM         [default: 2]
  * RAM_SIZE          - VM's RAM in MB              [default: 8192]
  * DISK_SIZE_DATA    - Data partition's size in GB [default: 120]
  * IMAGE_ROOTFS_SIZE - Root partition's size in KB [default: 8286208 (8096 MB)]¹
```

¹ If you overwrite IMAGE_ROOTFS_SIZE in your local.conf, it is recommended to use
  values of full GBs minus 100 MB.
  The build system will add 100 MB (for the boot partition) to IMAGE_ROOTFS_SIZE
  and use the result as the actual disk size requested from vmware.

### III. Changing Network Interface Types

Network interface type is set to e1000 by default. The udev rules are set for e1000
and vmxnet3 interfaces. No other interface types are supported. Use the same interface
type for all interfaces, mixing interface types is not supported.

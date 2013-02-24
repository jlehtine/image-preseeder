# Image Preseeder, a tool to create preseeded Linux installation images
# Copyright 2013 Johannes Lehtinen
#
# Licensed under the MIT License, see LICENSE.md.

# Name of the example and virtual machine used
EXAMPLE = ubuntu-mini-net
VM = image-preseed-examples-$(EXAMPLE)

# Ubuntu mini image to download
UBUNTU_MINI_URL = http://archive.ubuntu.com/ubuntu/dists/precise/main/installer-amd64/20101020ubuntu136/images/netboot/mini.iso

# Where to store large files
IMAGE_PRESEED_STORAGE ?= ../storage
UBUNTU_MINI_ISO = $(IMAGE_PRESEED_STORAGE)/ubuntu-12.04-mini.iso
UBUNTU_PRESEEDED_MINI_ISO = $(IMAGE_PRESEED_STORAGE)/ubuntu-12.04-mini-preseeded.iso
VMBASE = $(IMAGE_PRESEED_STORAGE)/vm-$(EXAMPLE)

examples: stamp-vm-updated

$(UBUNTU_ISO):
	false # TBD

$(UBUNTU_PRESEEDED_ISO): $(UBUNTU_ISO)
	false # TBD

stamp-vm-created:
	false # TBD

stamp-vm-installed: $(UBUNTU_PRESEEDED_ISO) stamp-vm-created
	false # TBD

stamp-vm-updated: stamp-vm-installed
	false # TBD

clean:
	false # TBD

.PHONY: examples clean

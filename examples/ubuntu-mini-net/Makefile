# Image Preseeder, a tool to create preseeded Linux installation images
# Copyright 2013 Johannes Lehtinen
#
# Licensed under the MIT License, see LICENSE.md.

# To support builds in a separate build directory
ifdef SOURCE_DIR
VPATH = $(SOURCE_DIR)
endif

# Settings for the example
IMAGE_PRESEEDER ?= image-preseeder
IMAGE_PRESEEDER_STORAGE ?= ../storage
IMAGE_PRESEEDER_VBEXECUTOR ?= VirtualBox
IMAGE_PRESEEDER_VBMANAGE ?= VBoxManage

# Shortcut names
VBOX = $(IMAGE_PRESEEDER_VBEXECUTOR)
VBOXMANAGE = $(IMAGE_PRESEEDER_VBMANAGE)

# Name of the example
EXAMPLE = ubuntu-mini-net

# Ubuntu mini image URL
UBUNTU_MINI_URL = http://archive.ubuntu.com/ubuntu/dists/precise/main/installer-amd64/20101020ubuntu136/images/netboot/mini.iso

# File and virtual machine names to use
UBUNTU_MINI_ISO = $(IMAGE_PRESEEDER_STORAGE)/ubuntu-12.04-mini.iso
UBUNTU_PRESEEDED_MINI_ISO = $(IMAGE_PRESEEDER_STORAGE)/ubuntu-12.04-mini-preseeded.iso
VMBASE = $(IMAGE_PRESEEDER_STORAGE)/vm-$(EXAMPLE)
VMNAME = image-preseed-examples-$(EXAMPLE)

examples: stamp-vm-updated

# ------------------------------------------------------------------------
# Download the original Ubuntu mini image
# ------------------------------------------------------------------------
$(UBUNTU_MINI_ISO):
	mkdir -p '$(IMAGE_PRESEEDER_STORAGE)'
	@echo "*** Downloading original Ubuntu mini image ***"
	wget -O '$@.tmp' '$(UBUNTU_MINI_URL)'
	mv '$@.tmp' '$@'

# ------------------------------------------------------------------------
# Create preseeded Ubuntu mini image
# ------------------------------------------------------------------------
$(UBUNTU_PRESEEDED_MINI_ISO): $(UBUNTU_MINI_ISO)
	@echo "*** Preseeding Ubuntu mini image ***"
	$(IMAGE_PRESEEDER) -v -o '$@' '$<'

# ------------------------------------------------------------------------
# Create virtual machine
# ------------------------------------------------------------------------
stamp-vm-created:
	@echo "*** Removing existing virtual machine, if any ***"
	vopts=''; \
	  test -z '$(SOURCE_DIR)' \
	    || vopts='-f '\''$(SOURCE_DIR)'\''/Makefile SOURCE_DIR='\''$(SOURCE_DIR)'\'; \
	  eval '$(MAKE)' $$vopts clean-vm
	@echo "*** Create and configure new virtual machine ***"
	'$(VBOXMANAGE)' createvm --name $(VMNAME) --ostype Ubuntu_64 --register --basefolder '$(VMBASE)'
	'$(VBOXMANAGE)' modifyvm $(VMNAME) --memory 512 --vram 12 --pae off --rtcuseutc on --mouse usbtablet
	'$(VBOXMANAGE)' storagectl $(VMNAME) --name SATA --add sata --sataportcount 4 
	@echo "*** Create and attach disk and DVD drive ***"
	'$(VBOXMANAGE)' createhd --filename '$(VMBASE)/disk.vdi' --format vdi --size 8192
	'$(VBOXMANAGE)' storageattach $(VMNAME) --storagectl SATA --port 0 --device 0 --type hdd --medium '$(VMBASE)/disk.vdi'
	'$(VBOXMANAGE)' storageattach $(VMNAME) --storagectl SATA --port 1 --device 0 --type dvddrive --medium emptydrive
	@echo "*** Take snapshot of the initial state ***"
	'$(VBOXMANAGE)' snapshot $(VMNAME) take created
	@echo "*** Create virtual machine created stamp ***"
	touch $@

# ------------------------------------------------------------------------
# Install Linux base installation
# ------------------------------------------------------------------------
stamp-vm-installed: $(UBUNTU_PRESEEDED_MINI_ISO) stamp-vm-created
	@echo "*** Restore snapshot of the initial state ***"
	'$(VBOXMANAGE)' snapshot $(VMNAME) restore created
	@echo "*** Attach installation media ***"
	'$(VBOXMANAGE)' storageattach $(VMNAME) --storagectl SATA --port 1 --device 0 --type dvddrive --medium '$(UBUNTU_PRESEEDED_MINI_ISO)'
	@echo "*** Running installation ***"
	'$(VBOX)' --startvm $(VMNAME)
	@echo "*** Detach installation media ***"
	'$(VBOXMANAGE)' storageattach $(VMNAME) --storagectl SATA --port 1 --device 0 --type dvddrive --medium emptydrive
	@echo "*** Take or replace snapshot of the installed state ***"
	-'$(VBOXMANAGE)' snapshot $(VMNAME) delete installed
	'$(VBOXMANAGE)' snapshot $(VMNAME) take installed
	@echo "*** Create virtual machine installed stamp ***"
	touch $@

# ------------------------------------------------------------------------
# Update virtual machine using external script
# ------------------------------------------------------------------------
stamp-vm-updated: stamp-vm-installed
	false # TBD

# ------------------------------------------------------------------------
# Clean
# ------------------------------------------------------------------------
clean: clean-examples

clean-examples: clean-preseeded clean-vm
	rm -f stamp-*

clean-preseeded: 
	rm -f $(UBUNTU_PRESEEDED_MINI_ISO)

clean-vm:
	-$(VBOXMANAGE) unregistervm $(VMNAME) --delete
	-$(VBOXMANAGE) closemedium disk $(VMBASE)/disk.vdi --delete
	rm -rf $(VMBASE)

.PHONY: examples clean clean-examples clean-preseeded clean-vm

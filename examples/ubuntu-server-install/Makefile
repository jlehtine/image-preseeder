# Image Preseeder, a tool to create preseeded Linux installation images
# Copyright 2013 Johannes Lehtinen
#
# Licensed under the MIT License, see LICENSE.md.

# To support builds in a separate build directory
ifdef SOURCE_DIR
    VPATH = $(SOURCE_DIR)
else
    SOURCE_DIR = $(shell pwd)
endif

# Settings for the example
IMAGE_PRESEEDER ?= image-preseeder
ABS_IMAGE_PRESEEDER = $(shell which '$(IMAGE_PRESEEDER'))
IMAGE_PRESEEDER_STORAGE ?= ../storage
IMAGE_PRESEEDER_VBEXECUTOR ?= VirtualBox
IMAGE_PRESEEDER_VBMANAGE ?= VBoxManage

# Shortcut names
VBOX = $(IMAGE_PRESEEDER_VBEXECUTOR)
VBOXMANAGE = $(IMAGE_PRESEEDER_VBMANAGE)

# Name of the example
EXAMPLE = ubuntu-server-install

# Ubuntu server image URL
UBUNTU_SERVER_URL = http://releases.ubuntu.com/12.04/ubuntu-12.04.2-server-i386.iso

# File and virtual machine names to use
UBUNTU_SERVER_ISO = $(IMAGE_PRESEEDER_STORAGE)/ubuntu-12.04.2-server-i386.iso
UBUNTU_SERVER_ISO_SHA256 = ubuntu-12.04.2-server-i386.sha256
UBUNTU_SERVER_ISO_PRESEEDED = $(IMAGE_PRESEEDER_STORAGE)/ubuntu-12.04.2-server-i386-preseeded.iso
VMBASE = $(IMAGE_PRESEEDER_STORAGE)/vm-$(EXAMPLE)
VMNAME = image-preseed-examples-$(EXAMPLE)

examples: stamp-vm-updated

# ------------------------------------------------------------------------
# Download the original Ubuntu server image
# ------------------------------------------------------------------------
$(UBUNTU_SERVER_ISO):
	mkdir -p '$(IMAGE_PRESEEDER_STORAGE)'
	@echo "*** Downloading original Ubuntu server image ***"
	wget -O '$@.tmp' '$(UBUNTU_SERVER_URL)'
	mv '$@.tmp' '$@'

# ------------------------------------------------------------------------
# Verify integrity of the server image
# ------------------------------------------------------------------------
stamp-verified-iso: $(UBUNTU_SERVER_ISO) $(UBUNTU_SERVER_ISO_SHA256)
	@echo '*** Verifying integrity of the downloaded image ***'
	cd '$(IMAGE_PRESEEDER_STORAGE)' \
	    && shasum -a 256 -c '$(SOURCE_DIR)/$(UBUNTU_SERVER_ISO_SHA256)'
	touch $@

# ------------------------------------------------------------------------
# Create preseeded Ubuntu server image
# ------------------------------------------------------------------------
$(UBUNTU_SERVER_ISO_PRESEEDED): $(UBUNTU_SERVER_ISO) stamp-verified-iso $(ABS_IMAGE_PRESEEDER)
	@echo "*** Preseeding Ubuntu server image ***"
	$(IMAGE_PRESEEDER) -v -o '$@' '$<'

# ------------------------------------------------------------------------
# Create virtual machine
# ------------------------------------------------------------------------
stamp-vm-created:
	@echo "*** Removing existing virtual machine, if any ***"
	vopts=''; \
	  test -z '$(VPATH)' \
	    || vopts='-f '\''$(SOURCE_DIR)'\''/Makefile SOURCE_DIR='\''$(SOURCE_DIR)'\'; \
	  eval '$(MAKE)' $$vopts clean-vm
	@echo "*** Create and configure new virtual machine ***"
	'$(VBOXMANAGE)' createvm --name $(VMNAME) --ostype Ubuntu --register --basefolder '$(VMBASE)'
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
# Install Ubuntu server base installation
# ------------------------------------------------------------------------
stamp-vm-installed: $(UBUNTU_SERVER_ISO_PRESEEDED) stamp-vm-created
	@echo "*** Restore snapshot of the initial state ***"
	'$(VBOXMANAGE)' snapshot $(VMNAME) restore created
	@echo "*** Attach installation media ***"
	'$(VBOXMANAGE)' storageattach $(VMNAME) --storagectl SATA --port 1 --device 0 --type dvddrive --medium '$(UBUNTU_SERVER_ISO_PRESEEDED)'
	@echo "*** Running Ubuntu server installation ***"
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
# Clean examples for re-run
# ------------------------------------------------------------------------
clean: clean-examples

clean-examples: clean-preseeded clean-vm

clean-preseeded: 
	rm -f $(UBUNTU_SERVER_ISO_PRESEEDED)

clean-vm:
	-$(VBOXMANAGE) unregistervm $(VMNAME) --delete
	-$(VBOXMANAGE) closemedium disk $(VMBASE)/disk.vdi --delete
	rm -rf $(VMBASE)
	rm -f stamp-*

.PHONY: examples clean clean-examples clean-preseeded clean-vm

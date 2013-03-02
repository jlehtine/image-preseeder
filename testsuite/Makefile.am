# Image Preseeder, a tool to create preseeded Linux installation images
# Copyright 2013 Johannes Lehtinen
#
# Licensed under the MIT License, see LICENSE.md.

# ISO images to be tested
TEST_IMAGE_URLS = \
http://cdimage.debian.org/debian-cd/6.0.7/i386/iso-cd/debian-6.0.7-i386-CD-1.iso \
http://cdimage.debian.org/debian-cd/6.0.7/amd64/iso-cd/debian-6.0.7-amd64-CD-1.iso \
http://releases.ubuntu.com/precise/ubuntu-12.04.2-desktop-i386.iso \
http://releases.ubuntu.com/precise/ubuntu-12.04.2-desktop-amd64.iso \
http://releases.ubuntu.com/precise/ubuntu-12.04.2-server-i386.iso \
http://releases.ubuntu.com/precise/ubuntu-12.04.2-server-amd64.iso \
http://releases.ubuntu.com/quantal/ubuntu-12.10-desktop-i386.iso \
http://releases.ubuntu.com/quantal/ubuntu-12.10-desktop-amd64.iso \
http://releases.ubuntu.com/quantal/ubuntu-12.10-server-i386.iso \
http://releases.ubuntu.com/quantal/ubuntu-12.10-server-amd64.iso 

# Use image-preseeder in build dir, unless otherwise configured
IMAGE_PRESEEDER ?= $(top_builddir)/image-preseeder
IMAGE_PRESEEDER_TOPSRCDIR ?= $(abs_top_srcdir)
IMAGE_PRESEEDER_PKGDATADIR ?= $(IMAGE_PRESEEDER_TOPSRCDIR)/data
IMAGE_PRESEEDER_DOCDIR ?= $(IMAGE_PRESEEDER_TOPSRCDIR)
export IMAGE_PRESEEDER IMAGE_PRESEEDER_TOPSRCDIR IMAGE_PRESEEDER_PKGDATADIR
export IMAGE_PRESEEDER_DOCDIR

# Default storage directory (can be overridden)
IMAGE_PRESEEDER_STORAGE ?= storage

# VirtualBox commands to use (can be overridden)
IMAGE_PRESEEDER_VBEXECUTOR ?= VirtualBox
IMAGE_PRESEEDER_VBMANAGE ?= VBoxManage
VBOX = $(IMAGE_PRESEEDER_VBEXECUTOR)
VBOXMANAGE = $(IMAGE_PRESEEDER_VBMANAGE)

check-local: clean-check check-incremental

clean-check:
	for TEST_IMAGE_URL in $(TEST_IMAGE_URLS); do \
	    TEST_NAME="`echo $$TEST_IMAGE_URL | sed -e 's/^.*\///' -e 's/\.[^\.]*$$//'`"; \
	    export TEST_NAME TEST_IMAGE_URL; \
	    $(MAKE) $(AM_MAKEFLAGS) clean-test || exit 1; \
	done

check-incremental: Makefile
	@tests=0; \
	errors=0; \
	echo ""; \
	echo "Running tests (this may take looooong time)..."; \
	for TEST_IMAGE_URL in $(TEST_IMAGE_URLS); do \
	    TEST_NAME="`echo $$TEST_IMAGE_URL | sed -e 's/^.*\///' -e 's/\.[^\.]*$$//'`"; \
	    export TEST_NAME TEST_IMAGE_URL; \
	    if $(MAKE) $(AM_MAKEFLAGS) check-test > test.log 2>&1; then \
		echo "PASS: $$TEST_NAME"; \
	    else \
		cat test.log; \
		echo "FAIL: $$TEST_NAME"; \
		echo ""; \
		errors=$$(($$errors + 1)); \
	    fi; \
	    tests=$$(($$tests + 1)); \
	done; \
	echo ""; \
	echo "==========================================================="; \
	test $$errors -eq 0 || echo "FAIL: Failed $$errors/$$tests tests"; \
	test ! $$errors -eq 0 || echo "PASS: Passed all $$tests tests"; \
	echo "==========================================================="; \
	test $$errors -eq 0

.PHONY: check-clean check-incremental clean-check


# ---------------------------------------------------------------------------
# Rest of the Makefile only applies to particular test
# ---------------------------------------------------------------------------

# TEST_NAME and TEST_IMAGE_URL should always be specified
TEST_NAME ?= unspecified
TEST_IMAGE_URL ?= http://nonexisting/nonexisting.iso

# Set some variables for individual test
IMAGE = $(IMAGE_PRESEEDER_STORAGE)/$(TEST_NAME).iso
IMAGE_SHA256 = $(srcdir)/checksums/$(TEST_NAME).sha256
PRESEEDED_IMAGE = $(IMAGE_PRESEEDER_STORAGE)/$(TEST_NAME)-preseeded.iso
VMBASE = $(IMAGE_PRESEEDER_STORAGE)/vm-test-$(TEST_NAME)
VMNAME = image-preseeder-test-$(TEST_NAME)

# Check is PASS if tests have been passed
check-test: stamp-$(TEST_NAME)-passed

# Download image
$(IMAGE):
	wget -nv -O '$@.tmp' $(TEST_IMAGE_URL)
	mv '$@.tmp' '$@'

# Verify image
stamp-$(TEST_NAME)-image-verified: $(IMAGE) $(IMAGE_SHA256)
	( cd '$(IMAGE_PRESEEDER_STORAGE)' && shasum -a 256 -c ) < $(IMAGE_SHA256)
	touch '$@'

# Preseed image
$(PRESEEDED_IMAGE): stamp-$(TEST_NAME)-image-verified $(IMAGE_PRESEEDER)
	$(IMAGE_PRESEEDER) -o '$@' -c virtualbox '$(IMAGE)'

# Create virtual machine
stamp-$(TEST_NAME)-vm-created:
	$(MAKE) $(AM_MAKEFLAGS) clean-test-vm
	case $(TEST_NAME) in \
	    debian-*amd64*) ostype=Debian_64 ;; \
	    debian-*) ostype=Debian ;; \
	    ubuntu-*amd64*) ostype=Ubuntu_64 ;; \
	    ubuntu-*) ostype=Ubuntu ;; \
	    *) exit 1 ;; \
	esac; \
	'$(VBOXMANAGE)' createvm --name $(VMNAME) --ostype $$ostype --register --basefolder '$(VMBASE)'
	'$(VBOXMANAGE)' modifyvm $(VMNAME) --memory 1024 --rtcuseutc on
	'$(VBOXMANAGE)' storagectl $(VMNAME) --name SATA --add sata --sataportcount 4 
	'$(VBOXMANAGE)' createhd --filename '$(VMBASE)/disk.vdi' --format vdi --size 8192
	'$(VBOXMANAGE)' storageattach $(VMNAME) --storagectl SATA --port 0 --device 0 --type hdd --medium '$(VMBASE)/disk.vdi'
	'$(VBOXMANAGE)' storageattach $(VMNAME) --storagectl SATA --port 1 --device 0 --type dvddrive --medium emptydrive
	'$(VBOXMANAGE)' snapshot $(VMNAME) take created
	touch '$@'

# Install preseeded image to virtual machine
stamp-$(TEST_NAME)-vm-installed: $(PRESEEDED_IMAGE) stamp-$(TEST_NAME)-vm-created
	'$(VBOXMANAGE)' snapshot $(VMNAME) restore created
	'$(VBOXMANAGE)' storageattach $(VMNAME) --storagectl SATA --port 1 --device 0 --type dvddrive --medium '$(PRESEEDED_IMAGE)' --mtype readonly
	'$(VBOX)' --startvm $(VMNAME)
	'$(VBOXMANAGE)' storageattach $(VMNAME) --storagectl SATA --port 1 --device 0 --type dvddrive --medium emptydrive
	-'$(VBOXMANAGE)' snapshot $(VMNAME) delete installed > /dev/null 2>&1
	'$(VBOXMANAGE)' snapshot $(VMNAME) take installed
	touch '$@'

# Test external control
stamp-$(TEST_NAME)-passed: stamp-$(TEST_NAME)-vm-installed

# Cleans the test
clean-test: clean-test-vm
	rm -f stamp-$(TEST_NAME)*
	rm -f $(PRESEEDED_IMAGE)

# Removes the virtual machine associated with the test
clean-test-vm:
	$(VBOXMANAGE) unregistervm $(VMNAME) --delete > /dev/null 2>&1 || true
	$(VBOXMANAGE) closemedium disk $(VMBASE)/disk.vdi --delete > /dev/null 2>&1 || true
	rm -rf $(VMBASE)

.PHONY: check-test clean-test clean-test-vm

# Image Preseeder, a tool to create preseeded Linux installation images
# Copyright 2013 Johannes Lehtinen
#
# Licensed under the MIT License, see LICENSE.md.

bin_SCRIPTS = image-preseeder
dist_doc_DATA = README.md LICENSE.md
dist_pkgdata_DATA = \
	data/preseed-ubuntu-server-12.04-normal.cfg \
	data/include-ubuntu-server-12.04-normal

EXAMPLE_TARGETS = examples clean-examples

# Environment variables for the examples when started from the build tree
IMAGE_PRESEEDER ?= $(abs_top_builddir)/image-preseeder
IMAGE_PRESEEDER_PKGDATADIR ?= $(abs_top_srcdir)/data
IMAGE_PRESEEDER_DOCDIR ?= $(abs_top_srcdir)
export IMAGE_PRESEEDER IMAGE_PRESEEDER_PKGDATADIR IMAGE_PRESEEDER_DOCDIR

image-preseeder: $(srcdir)/image-preseeder.in Makefile
	rm -f $@.tmp
	sed -e 's|@pkglibdir[@]|$(pkglibdir)|g' \
	  -e 's|@pkgdatadir[@]|$(pkgdatadir)|g' \
	  -e 's|@docdir[@]|$(docdir)|g' \
	  < '$(srcdir)'/$@.in > $@.tmp
	chmod +x $@.tmp
	chmod go-w $@.tmp
	mv $@.tmp $@

clean-local:
	-rm -f image-preseeder

examples: image-preseeder

$(EXAMPLE_TARGETS):
	mkdir -p examples
	vopts=''; \
	test '$(srcdir)' = . \
	  || vopts='-f '\''$(abs_srcdir)'\''/examples/Makefile SOURCE_DIR='\''$(abs_srcdir)'\''/examples'; \
	eval '$(MAKE)' -C examples $$vopts $@

.PHONY: $(EXAMPLE_TARGETS)

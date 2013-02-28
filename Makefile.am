# Image Preseeder, a tool to create preseeded Linux installation images
# Copyright 2013 Johannes Lehtinen
#
# Licensed under the MIT License, see LICENSE.md.

bin_SCRIPTS = image-preseeder
dist_doc_DATA = README.md LICENSE.md EXAMPLES.md
dist_pkgdata_DATA = \
	data/preseed-ubuntu-server-12.04-normal.cfg \
	data/include-ubuntu-server-12.04-normal

EXAMPLE_TARGETS = examples clean-examples
EXAMPLES_MAKEFILES = examples/Makefile examples/ubuntu-server-install/Makefile

# Environment variables for the examples when started from the build tree
IMAGE_PRESEEDER ?= $(abs_builddir)/image-preseeder
IMAGE_PRESEEDER_TOPSRCDIR ?= $(abs_top_srcdir)
IMAGE_PRESEEDER_PKGDATADIR ?= $(IMAGE_PRESEEDER_TOPSRCDIR)/data
IMAGE_PRESEEDER_DOCDIR ?= $(IMAGE_PRESEEDER_TOPSRCDIR)
export IMAGE_PRESEEDER IMAGE_PRESEEDER_TOPSRCDIR IMAGE_PRESEEDER_PKGDATADIR
export IMAGE_PRESEEDER_DOCDIR

all-local: image-preseeder $(EXAMPLES_MAKEFILES)

clean-local:
	-rm -f image-preseeder
	-rm -f $(EXAMPLES_MAKEFILES)

examples: image-preseeder

$(EXAMPLE_TARGETS): $(EXAMPLES_MAKEFILES)
	$(MAKE) -C examples IMAGE_PRESEEDER_SOURCE=$(abs_srcdir) $@

%: %.template
	rm -f '$@.tmp'
	mkdir -p "`dirname '$@'`"
	sed -e 's|@pkglibdir[@]|$(pkglibdir)|g' \
	  -e 's|@pkgdatadir[@]|$(pkgdatadir)|g' \
	  -e 's|@docdir[@]|$(docdir)|g' \
	  < '$<' > '$@.tmp'
	chmod --reference='$<' '$@.tmp'
	mv '$@.tmp' '$@'

.PHONY: $(EXAMPLE_TARGETS)

# Image Preseeder, a tool to create preseeded Linux installation images
# Copyright 2013 Johannes Lehtinen
#
# Licensed under the MIT License, see LICENSE.md.

EXAMPLES = ubuntu-mini-net

examples clean maintainer-clean:
	for example in $(EXAMPLES); do \
	  mkdir -p $$example || exit 1; \
	  vopts=''; \
	    test -f $$example/Makefile \
	      || test -z "$(abs_srcdir)" \
	      || vopts="-f $(abs_srcdir)/$$example/Makefile abs_srcdir=$(abs_srcdir)/$$example VPATH=$(abs_srcdir)/$$example"; \
	  $(MAKE) -C $$example $$vopts $@ || exit 1; \
	done

.PHONY: examples clean maintainer-clean

# Image Preseeder, a tool to create preseeded Linux installation images
# Copyright 2013 Johannes Lehtinen
#
# Licensed under the MIT License, see LICENSE.md.

# To support builds in a separate build directory
ifdef SOURCE_DIR
VPATH = $(SOURCE_DIR)
endif

# List of examples
EXAMPLES = ubuntu-server-install

# Targets used for examples
EXAMPLE_TARGETS = examples clean-examples

$(EXAMPLE_TARGETS):
	for example in $(EXAMPLES); do \
	  mkdir -p $$example || exit 1; \
	  vopts=''; \
	  test -z '$(SOURCE_DIR)' \
	    || vopts='-f '\''$(SOURCE_DIR)'\''/$$example/Makefile SOURCE_DIR='\''$(SOURCE_DIR)'\''/$$example'; \
	  eval '$(MAKE)' -C $$example $$vopts $@ || exit 1; \
	done

.PHONY: $(EXAMPLE_TARGETS)

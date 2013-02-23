# Image Preseeder, a tool to create preseeded Linux installation images
# Copyright 2013 Johannes Lehtinen
#
# Licensed under the MIT License, see LICENSE.md.

bin_SCRIPTS = image-preseeder

image-preseeder: $(srcdir)/image-preseeder.in Makefile
	rm -f $@.tmp
	sed -e 's|@pkglibdir[@]|$(pkglibdir)|g' \
	  -e 's|@docdir[@]|$(docdir)|g' \
	  < $(srcdir)/$@.in > $@.tmp
	chmod +x $@.tmp
	chmod go-w $@.tmp
	mv $@.tmp $@

# vim:ai

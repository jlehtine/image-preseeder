# Image Preseeder, a tool to create preseeded Linux installation images
# Copyright 2013 Johannes Lehtinen
#
# Licensed under the MIT License, see LICENSE.md.

bin_SCRIPTS = image-preseeder
dist_doc_DATA = README.md LICENSE.md
dist_pkgdata_DATA = \
	lib/preseed-ubuntuserver-12.04-normal.cfg \
	lib/preseed-ubuntu-12.04-mini.cfg

image-preseeder: $(srcdir)/image-preseeder.in Makefile
	rm -f $@.tmp
	sed -e 's|@pkglibdir[@]|$(pkglibdir)|g' \
	  -e 's|@pkgdatadir[@]|$(pkgdatadir)|g' \
	  -e 's|@docdir[@]|$(docdir)|g' \
	  < $(srcdir)/$@.in > $@.tmp
	chmod +x $@.tmp
	chmod go-w $@.tmp
	mv $@.tmp $@

clean-local:
	-rm -f image-preseeder

# vim:ai

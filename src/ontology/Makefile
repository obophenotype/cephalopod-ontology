# This Makefile is for use by the CEPH Release Manager.
#
# requirements: Oort (http://code.google.com/p/owltools/wiki/OortIntro)
#
# To make a release, type 'make release' in this directory

build: ceph.obo minidiff
release: ceph.obo diff

# all release files are derived from the editors version.
# we use the default Oort setup (hermit as reasoner)
ceph.obo: ceph-edit.owl
	ontology-release-runner --catalog-xml catalog-v001.xml $< --allow-overwrite --asserted --simple


# ----------------------------------------
# Diffs
# ----------------------------------------

# 
diff: ceph.obo
	cd diffs && touch ceph-diff && rm ceph-*diff* && make

# Minidiff: intra-release differences
# TESTING: emailing self, will change to list
minidiff: ceph.obo
	cd diffs  && touch ceph-diff && rm ceph-*diff* && make TGTS='html txt' SRC=../ceph.obo && mail -s ceph-diffs cjmungall@lbl.gov < ceph-combined-diff.txt

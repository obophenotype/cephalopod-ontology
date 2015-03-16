OBO=http://purl.obolibrary.org/obo
USECAT= --catalog-xml catalog-v001.xml
RELEASE = $(OBO)/ceph/releases/`date +%Y-%m-%d`

# This Makefile is for use by the CEPH Release Manager.
#
# requirements: Oort (http://code.google.com/p/owltools/wiki/OortIntro)
#
# To make a release, type 'make release' in this directory

build: ceph.obo minidiff
release: ceph.obo diff

ceph.owl: ceph-edit.owl
	owltools --use-catalog $< --run-reasoner -r elk --assert-implied --set-ontology-id -v $(RELEASE)/$@ $(OBO)/$@ -o $@
#	owltools --use-catalog $< --remove-axioms --assert-inferred-subclass-axioms -o $@

ceph.obo: ceph.owl
	owltools --use-catalog $< --merge-imports-closure -o -f obo --no-check $@

# all release files are derived from the editors version.
# we use the default Oort setup (hermit as reasoner)
#ceph.obo: ceph-edit.owl
#	ontology-release-runner --catalog-xml catalog-v001.xml $< --allow-overwrite --asserted --simple

# ----------------------------------------
# Imports
# ----------------------------------------

# Base URI for local subset imports
CEPH_IMPORTS_BASE_URI = $(OBO)/ceph/imports

KEEPRELS = BFO:0000050 BFO:0000051 RO:0002202 immediate_transformation_of

# Create an import module using the OWLAPI module extraction code via OWLTools.
# We use the standard catalog, but rewrite the import to X to be a local mirror of ALL of X.
# After extraction, we further reduce the ontology by creating a "mingraph" (removes all annotations except label) and by 
imports/%_import.owl: ceph-edit.owl mirror/%.owl
	owltools  $(USECAT) --map-ontology-iri $(CEPH_IMPORTS_BASE_URI)/$*_import.owl mirror/$*.owl $<  --extract-module -s $(OBO)/$*.owl -c --remove-axiom-annotations --make-subset-by-properties $(KEEPRELS) --remove-annotation-assertions -l -s --set-ontology-id $(CEPH_IMPORTS_BASE_URI)/$@ -o $@

# clone remote ontology locally, perfoming some excision of relations and annotations
mirror/%.owl:
	owltools $(OBO)/$*.owl --remove-annotation-assertions -l --remove-dangling-annotations --make-subset-by-properties $(KEEPRELS) --set-ontology-id $(OBO)/$*.owl -o $@

# ----------------------------------------
# Reports
# ----------------------------------------
%.tbl: %.owl
	owljs-tableify -c -o $@ $<

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

# ----------------------------------------
# Bridges
# ----------------------------------------
core.owl: ceph.owl
	owltools --use-catalog $< --extract-bridge-ontologies -x -d . -s ceph --remove-imports-declarations --set-ontology-id $(OBO)/ceph/core.owl -o core.owl

# ----------------------------------------
# MARKDOWN EXPORT
# ----------------------------------------
markdown: ceph.owl
	owltools $< --merge-imports-closure --ontology-to-markdown md && git add md/*/*/*.md


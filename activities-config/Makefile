# Activities Configurator Makefile

# Before making zip file insure all changes have been committed.
# README.txt is modified to indicate the date and time the zip file was
# created and the current git commit.  VERSION should be set to the
# current extension version.
.ONESHELL:
UUID = activities-config@nls1729
MODULES = metadata.json README.txt colors.js convenience.js COPYING extension.js face-smile-3.svg keys.js \
          notify.js prefs.js readme.js stylesheet.css message.pot
POFILES = $(wildcard po/*.po)
JSFILES = colors.js extension.js keys.js notify.js prefs.js readme.js
ZIPDATE = $(shell date)
COMMIT = $(shell git rev-parse HEAD)
VERSION = $(shell cat ./VERSION)

all: extension

extension: ./schemas/gschemas.compiled $(POFILES:.po=.mo)

./schemas/gschemas.compiled: ./schemas/org.gnome.shell.extensions.activities-config.gschema.xml
	glib-compile-schemas ./schemas/

./po/%.mo: ./po/%.po
	msgfmt -c $< -o $@

_build: all
	rm -rf ./_build
	mkdir -p _build
	xgettext $(JSFILES) -o message.pot;
	sed -i 's/"version":.*$\/"version": $(VERSION)/' metadata.json;
	sed -i 's/^zip file:.*$\/zip file: $(ZIPDATE) $(COMMIT)/'  README.txt;
	sed -i 's/"Commit: .*$\/"Commit: $(COMMIT)";/'  prefs.js;
	cp $(MODULES) _build
	mkdir -p _build/schemas
	cp schemas/*.xml _build/schemas/
	cp schemas/gschemas.compiled _build/schemas/
	mkdir -p _build/locale
	for langmo in $(POFILES:.po=.mo) ; do \
		langdir=_build/locale/`basename $$langmo .mo`; \
		mkdir -p $$langdir; \
		mkdir -p $$langdir/LC_MESSAGES; \
		cp $$langmo $$langdir/LC_MESSAGES/activities-config-extension.mo; \
	done;

zip-file: _build
	cd _build ; \
	zip -qr "$(UUID).zip" .; \
	mv $(UUID).zip .. ; \
	cd .. ;
	sha256sum "$(UUID).zip"  > CHECKSUM
	rm -rf _build

touch:
	for file in $(MODULES); do \
		touch $$file; \
	done;

clean:
	rm -f ./schemas/gschemas.compiled
	rm -f ./po/*.mo
	rm -f ./*.zip
	rm -f ./.~
	rm -f ./*~
	rm -rf _build


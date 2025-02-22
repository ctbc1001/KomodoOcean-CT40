ACLOCAL_AMFLAGS = -I build-aux/m4
SUBDIRS = src
if ENABLE_MAN
SUBDIRS += doc/man
endif
.PHONY: deploy FORCE

if BUILD_KOMODO_LIBS
pkgconfigdir = $(libdir)/pkgconfig
pkgconfig_DATA = libzcashconsensus.pc
endif

KOMODOD_BIN=$(top_builddir)/src/$(BITCOIN_DAEMON_NAME)$(EXEEXT)
KOMODO_QT_BIN=$(top_builddir)/src/qt/$(BITCOIN_GUI_NAME)$(EXEEXT)
KOMODO_CLI_BIN=$(top_builddir)/src/$(BITCOIN_CLI_NAME)$(EXEEXT)
KOMODO_TX_BIN=$(top_builddir)/src/$(BITCOIN_TX_NAME)$(EXEEXT)
KOMODO_WALLET_BIN=$(top_builddir)/src/$(BITCOIN_WALLET_TOOL_NAME)$(EXEEXT)
KOMODO_WIN_INSTALLER=$(PACKAGE_NAME)-$(PACKAGE_VERSION)-win$(WINDOWS_BITS)-setup$(EXEEXT)

empty :=
space := $(empty) $(empty)

if TARGET_DARWIN
OSX_APP=Komodo-Qt.app
#OSX_VOLNAME = $(subst $(space),-,$(PACKAGE_NAME))
OSX_VOLNAME = $(subst $(space),-,$(PACKAGE_NAME)-$(PACKAGE_VERSION))
OSX_DMG = $(OSX_VOLNAME).dmg
OSX_BACKGROUND_SVG=background.svg
OSX_BACKGROUND_IMAGE=background.tiff
OSX_BACKGROUND_IMAGE_DPIS=36 72
OSX_DSSTORE_GEN=$(top_srcdir)/contrib/macdeploy/custom_dsstore.py
OSX_DEPLOY_SCRIPT=$(top_srcdir)/contrib/macdeploy/macdeployqtplus
OSX_FANCY_PLIST=$(top_srcdir)/contrib/macdeploy/fancy.plist
# OSX_BASE_LPROJ_DIR=$(top_srcdir)/contrib/macdeploy/Base.lproj/InfoPlist.strings
OSX_INSTALLER_ICONS=$(top_srcdir)/src/qt/res/icons/CT40.icns
OSX_PLIST=$(top_srcdir)/share/qt/Info.plist #not installed
OSX_QT_TRANSLATIONS = da,de,es,hu,ru,uk,zh_CN,zh_TW
endif

DIST_DOCS = $(wildcard doc/*.md) $(wildcard doc/release-notes/*.md)

BIN_CHECKS=$(top_srcdir)/contrib/devtools/symbol-check.py \
           $(top_srcdir)/contrib/devtools/security-check.py

WINDOWS_PACKAGING = $(top_srcdir)/share/pixmaps/CT40.ico \
	$(top_srcdir)/share/pixmaps/nsis-header.bmp \
	$(top_srcdir)/share/pixmaps/nsis-wizard.bmp

if TARGET_DARWIN

# OSX_PACKAGING = $(OSX_DEPLOY_SCRIPT) $(OSX_FANCY_PLIST) $(OSX_INSTALLER_ICONS) $(OSX_BASE_LPROJ_DIR) \
#   $(top_srcdir)/contrib/macdeploy/$(OSX_BACKGROUND_IMAGE) \
#   $(top_srcdir)/contrib/macdeploy/DS_Store \
#   $(top_srcdir)/contrib/macdeploy/detached-sig-apply.sh \
#   $(top_srcdir)/contrib/macdeploy/detached-sig-create.sh
OSX_PACKAGING = $(OSX_DEPLOY_SCRIPT) $(OSX_FANCY_PLIST) $(OSX_INSTALLER_ICONS) \
	$(top_srcdir)/contrib/macdeploy/$(OSX_BACKGROUND_SVG) \
	$(OSX_DSSTORE_GEN) \
	$(top_srcdir)/contrib/macdeploy/detached-sig-apply.sh \
	$(top_srcdir)/contrib/macdeploy/detached-sig-create.sh
endif

if TARGET_DARWIN
COVERAGE_INFO = baseline_filtered_combined.info baseline.info block_test.info \
  leveldb_baseline.info test_komodo_filtered.info total_coverage.info \
  baseline_filtered.info block_test_filtered.info \
  leveldb_baseline_filtered.info test_komodo_coverage.info test_komodo.info
#  zcash-gtest.info zcash-gtest_filtered.info zcash-gtest_coverage.info
else
COVERAGE_INFO = baseline_filtered_combined.info baseline.info block_test.info \
  leveldb_baseline.info test_komodo_filtered.info total_coverage.info \
  baseline_filtered.info block_test_filtered.info \
  leveldb_baseline_filtered.info test_komodo_coverage.info test_komodo.info \
  #zcash-gtest.info zcash-gtest_filtered.info zcash-gtest_coverage.info
endif

dist-hook:
	-$(GIT) archive --format=tar HEAD -- src/clientversion.cpp | $(AMTAR) -C $(top_distdir) -xf -

$(KOMODO_WIN_INSTALLER): all-recursive
	$(MKDIR_P) $(top_builddir)/release
	STRIPPROG="$(STRIP)" $(INSTALL_STRIP_PROGRAM) $(KOMODOD_BIN) $(top_builddir)/release
	STRIPPROG="$(STRIP)" $(INSTALL_STRIP_PROGRAM) $(KOMODO_QT_BIN) $(top_builddir)/release
	STRIPPROG="$(STRIP)" $(INSTALL_STRIP_PROGRAM) $(KOMODO_CLI_BIN) $(top_builddir)/release
	STRIPPROG="$(STRIP)" $(INSTALL_STRIP_PROGRAM) $(KOMODO_TX_BIN) $(top_builddir)/release
	STRIPPROG="$(STRIP)" $(INSTALL_STRIP_PROGRAM) $(KOMODO_WALLET_BIN) $(top_builddir)/release

	@test -f $(MAKENSIS) && $(MAKENSIS) -V2 $(top_builddir)/share/setup.nsi || \
	  echo error: could not build $@
	@echo built $@

$(if $(findstring src/,$(MAKECMDGOALS)),$(MAKECMDGOALS), none): FORCE
	$(MAKE) -C src $(patsubst src/%,%,$@)

if TARGET_DARWIN

$(OSX_APP)/Contents/PkgInfo:
	$(MKDIR_P) $(@D)
	@echo "APPL????" > $@

$(OSX_APP)/Contents/Resources/empty.lproj:
	$(MKDIR_P) $(@D)
	@touch $@

$(OSX_APP)/Contents/Info.plist: $(OSX_PLIST)
	$(MKDIR_P) $(@D)
	$(INSTALL_DATA) $< $@

$(OSX_APP)/Contents/Resources/CT40.icns: $(OSX_INSTALLER_ICONS)
	$(MKDIR_P) $(@D)
	$(INSTALL_DATA) $< $@

# $(OSX_APP)/Contents/MacOS/Komodo-Qt: $(KOMODO_QT_BIN)
# 	$(MKDIR_P) $(@D)
# 	STRIPPROG="$(STRIP)" $(INSTALL_STRIP_PROGRAM)  $< $@

$(OSX_APP)/Contents/MacOS/Komodo-Qt: $(KOMODO_QT_BIN)
	$(MKDIR_P) $(@D)
	STRIPPROG="$(STRIP)" $(INSTALL_STRIP_PROGRAM) $< $@

# $(OSX_APP)/Contents/Resources/Base.lproj/InfoPlist.strings: $(OSX_BASE_LPROJ_DIR)
# 	$(MKDIR_P) $(@D)
# 	$(INSTALL_DATA) $< $@
$(OSX_APP)/Contents/Resources/Base.lproj/InfoPlist.strings:
	$(MKDIR_P) $(@D)
	echo '{	CFBundleDisplayName = "$(PACKAGE_NAME)"; CFBundleName = "$(PACKAGE_NAME)"; }' > $@

OSX_APP_BUILT=$(OSX_APP)/Contents/PkgInfo $(OSX_APP)/Contents/Resources/empty.lproj \
	$(OSX_APP)/Contents/Resources/CT40.icns $(OSX_APP)/Contents/Info.plist \
	$(OSX_APP)/Contents/MacOS/Komodo-Qt $(OSX_APP)/Contents/Resources/Base.lproj/InfoPlist.strings

endif

if BUILD_DARWIN
# $(OSX_DMG): $(OSX_APP_BUILT) $(OSX_PACKAGING)
# 	$(OSX_DEPLOY_SCRIPT) $(OSX_APP) -add-qt-tr $(OSX_QT_TRANSLATIONS) -translations-dir=$(QT_TRANSLATION_DIR) -dmg -fancy $(OSX_FANCY_PLIST) -verbose 2
$(OSX_DMG): $(OSX_APP_BUILT) $(OSX_PACKAGING) $(OSX_BACKGROUND_IMAGE)
	$(PYTHON) $(OSX_DEPLOY_SCRIPT) $(OSX_APP) -add-qt-tr $(OSX_QT_TRANSLATIONS) -translations-dir=$(QT_TRANSLATION_DIR) -dmg -fancy $(OSX_FANCY_PLIST) -verbose 2 -volname $(OSX_VOLNAME)

$(OSX_BACKGROUND_IMAGE).png: contrib/macdeploy/$(OSX_BACKGROUND_SVG)
	sed 's/PACKAGE_NAME/$(PACKAGE_NAME)/' < "$<" | $(RSVG_CONVERT) -f png -d 36 -p 36 -o $@
$(OSX_BACKGROUND_IMAGE)@2x.png: contrib/macdeploy/$(OSX_BACKGROUND_SVG)
	sed 's/PACKAGE_NAME/$(PACKAGE_NAME)/' < "$<" | $(RSVG_CONVERT) -f png -d 72 -p 72 -o $@
$(OSX_BACKGROUND_IMAGE): $(OSX_BACKGROUND_IMAGE).png $(OSX_BACKGROUND_IMAGE)@2x.png
	tiffutil -cathidpicheck $^ -out $@

deploydir: $(OSX_DMG)
else
APP_DIST_DIR=$(top_builddir)/dist
APP_DIST_EXTRAS=$(APP_DIST_DIR)/.background/$(OSX_BACKGROUND_IMAGE) $(APP_DIST_DIR)/.DS_Store $(APP_DIST_DIR)/Applications
#endif

#if TARGET_DARWIN
$(APP_DIST_DIR)/Applications:
	@rm -f $@
	@cd $(@D); $(LN_S) /Applications $(@F)

$(APP_DIST_EXTRAS): $(APP_DIST_DIR)/$(OSX_APP)/Contents/MacOS/Komodo-Qt

$(OSX_DMG): $(APP_DIST_EXTRAS)
	$(GENISOIMAGE) -no-cache-inodes -D -l -probe -V "$(OSX_VOLNAME)" -no-pad -r -dir-mode 0755 -apple -o $@ dist

# $(APP_DIST_DIR)/.background/$(OSX_BACKGROUND_IMAGE): contrib/macdeploy/$(OSX_BACKGROUND_IMAGE)
# 	$(MKDIR_P) $(@D)
# 	$(INSTALL) $< $@
dpi%.$(OSX_BACKGROUND_IMAGE): contrib/macdeploy/$(OSX_BACKGROUND_SVG)
	sed 's/PACKAGE_NAME/$(PACKAGE_NAME)/' < "$<" | $(RSVG_CONVERT) -f png -d $* -p $* | $(IMAGEMAGICK_CONVERT) - $@
OSX_BACKGROUND_IMAGE_DPIFILES := $(foreach dpi,$(OSX_BACKGROUND_IMAGE_DPIS),dpi$(dpi).$(OSX_BACKGROUND_IMAGE))
$(APP_DIST_DIR)/.background/$(OSX_BACKGROUND_IMAGE): $(OSX_BACKGROUND_IMAGE_DPIFILES)
	$(MKDIR_P) $(@D)
	$(TIFFCP) -c none $(OSX_BACKGROUND_IMAGE_DPIFILES) $@

$(APP_DIST_DIR)/.DS_Store: contrib/macdeploy/DS_Store
	echo $(PYTHON) $< "$@" "$(OSX_VOLNAME)"
	$(INSTALL) $< $@

# $(APP_DIST_DIR)/.DS_Store: $(OSX_DSSTORE_GEN)
# 	$(PYTHON) $< "$@" "$(OSX_VOLNAME)"

$(APP_DIST_DIR)/$(OSX_APP)/Contents/MacOS/Komodo-Qt: $(OSX_APP_BUILT) $(OSX_PACKAGING)
	INSTALLNAMETOOL=$(INSTALLNAMETOOL)  OTOOL=$(OTOOL) STRIP=$(STRIP) $(OSX_DEPLOY_SCRIPT) $(OSX_APP) -translations-dir=$(QT_TRANSLATION_DIR) -add-qt-tr $(OSX_QT_TRANSLATIONS) -verbose 2

deploydir: $(APP_DIST_EXTRAS)
endif

if TARGET_DARWIN
appbundle: $(OSX_APP_BUILT)
deploy: $(OSX_DMG)
endif

if TARGET_WINDOWS
deploy: $(KOMODO_WIN_INSTALLER)
endif

$(KOMODOD_BIN): FORCE
	$(MAKE) -C src $(@F)

$(KOMODO_CLI_BIN): FORCE
	$(MAKE) -C src $(@F)

if USE_LCOV

baseline.info:
	$(LCOV) -c -i -d $(abs_builddir)/src -o $@

if BUILD_DARWIN
baseline_filtered.info: baseline.info
	$(LCOV) -r $< "/usr/include/*" \
                      "$(abs_builddir)/depends/$(BUILD)/include/*.h" \
                      "$(abs_builddir)/depends/$(BUILD)/include/boost/*" \
                      "$(abs_builddir)/depends/$(BUILD)/include/gmock/*" \
                      "$(abs_builddir)/depends/$(BUILD)/include/gtest/*" \
                      "$(abs_builddir)/src/gtest/*" \
                      "$(abs_builddir)/src/test/*" \
                      "$(abs_builddir)/src/wallet/gtest/*" \
                      "$(abs_builddir)/src/wallet/test/*" \
                      -o $@
else
baseline_filtered.info: baseline.info
	$(LCOV) -r $< "/usr/include/*" \
                      "$(abs_builddir)/depends/x86_64-unknown-linux-gnu/include/*.h" \
                      "$(abs_builddir)/depends/x86_64-unknown-linux-gnu/include/boost/*" \
                      "$(abs_builddir)/depends/x86_64-unknown-linux-gnu/include/gmock/*" \
                      "$(abs_builddir)/depends/x86_64-unknown-linux-gnu/include/gtest/*" \
                      "$(abs_builddir)/src/gtest/*" \
                      "$(abs_builddir)/src/test/*" \
                      "$(abs_builddir)/src/wallet/gtest/*" \
                      "$(abs_builddir)/src/wallet/test/*" \
                      -o $@
endif

leveldb_baseline.info: baseline_filtered.info
	$(LCOV) -c -i -d $(abs_builddir)/src/leveldb -b $(abs_builddir)/src/leveldb -o $@

if BUILD_DARWIN
leveldb_baseline_filtered.info: leveldb_baseline.info
	$(LCOV) -r $< "/usr/include/*" \
                      "$(abs_builddir)/depends/$(BUILD)/include/*.h" \
                      "$(abs_builddir)/depends/$(BUILD)/include/boost/*" \
                      "$(abs_builddir)/depends/$(BUILD)/include/gmock/*" \
                      "$(abs_builddir)/depends/$(BUILD)/include/gtest/*" \
                      "$(abs_builddir)/src/gtest/*" \
                      "$(abs_builddir)/src/test/*" \
                      "$(abs_builddir)/src/wallet/gtest/*" \
                      "$(abs_builddir)/src/wallet/test/*" \
                      -o $@
else
leveldb_baseline_filtered.info: leveldb_baseline.info
	$(LCOV) -r $< "/usr/include/*" \
                      "$(abs_builddir)/depends/x86_64-unknown-linux-gnu/include/*.h" \
                      "$(abs_builddir)/depends/x86_64-unknown-linux-gnu/include/boost/*" \
                      "$(abs_builddir)/depends/x86_64-unknown-linux-gnu/include/gmock/*" \
                      "$(abs_builddir)/depends/x86_64-unknown-linux-gnu/include/gtest/*" \
                      "$(abs_builddir)/src/gtest/*" \
                      "$(abs_builddir)/src/test/*" \
                      "$(abs_builddir)/src/wallet/gtest/*" \
                      "$(abs_builddir)/src/wallet/test/*" \
                      -o $@
endif

baseline_filtered_combined.info: leveldb_baseline_filtered.info baseline_filtered.info
	$(LCOV) -a leveldb_baseline_filtered.info -a baseline_filtered.info -o $@

test_komodo.info: baseline_filtered_combined.info
	$(MAKE) -C src/ komodo_test_check
	$(LCOV) -c -d $(abs_builddir)/src -t test_komodo -o $@
	$(LCOV) -z -d $(abs_builddir)/src
	$(LCOV) -z -d $(abs_builddir)/src/leveldb

if BUILD_DARWIN
test_komodo_filtered.info: test_komodo.info
	$(LCOV) -r $< "/usr/include/*" \
                      "$(abs_builddir)/depends/$(BUILD)/include/*.h" \
                      "$(abs_builddir)/depends/$(BUILD)/include/boost/*" \
                      "$(abs_builddir)/depends/$(BUILD)/include/gmock/*" \
                      "$(abs_builddir)/depends/$(BUILD)/include/gtest/*" \
                      "$(abs_builddir)/src/gtest/*" \
                      "$(abs_builddir)/src/test/*" \
                      "$(abs_builddir)/src/wallet/gtest/*" \
                      "$(abs_builddir)/src/wallet/test/*" \
                      -o $@
else
test_komodo_filtered.info: test_komodo.info
	$(LCOV) -r $< "/usr/include/*" \
                      "$(abs_builddir)/depends/x86_64-unknown-linux-gnu/include/*.h" \
                      "$(abs_builddir)/depends/x86_64-unknown-linux-gnu/include/boost/*" \
                      "$(abs_builddir)/depends/x86_64-unknown-linux-gnu/include/gmock/*" \
                      "$(abs_builddir)/depends/x86_64-unknown-linux-gnu/include/gtest/*" \
                      "$(abs_builddir)/src/gtest/*" \
                      "$(abs_builddir)/src/test/*" \
                      "$(abs_builddir)/src/wallet/gtest/*" \
                      "$(abs_builddir)/src/wallet/test/*" \
                      -o $@
endif

block_test.info: test_komodo_filtered.info
	$(MKDIR_P) qa/tmp
	-@TIMEOUT=15 qa/pull-tester/run-komodod-for-test.sh $(JAVA) -jar $(JAVA_COMPARISON_TOOL) qa/tmp/compTool 0
	$(LCOV) -c -d $(abs_builddir)/src --t KomodoJBlockTest -o $@
	$(LCOV) -z -d $(abs_builddir)/src
	$(LCOV) -z -d $(abs_builddir)/src/leveldb

if BUILD_DARWIN
block_test_filtered.info: block_test.info
	$(LCOV) -r $< "/usr/include/*" \
                      "$(abs_builddir)/depends/$(BUILD)/include/*.h" \
                      "$(abs_builddir)/depends/$(BUILD)/include/boost/*" \
                      "$(abs_builddir)/depends/$(BUILD)/include/gmock/*" \
                      "$(abs_builddir)/depends/$(BUILD)/include/gtest/*" \
                      "$(abs_builddir)/src/gtest/*" \
                      "$(abs_builddir)/src/test/*" \
                      "$(abs_builddir)/src/wallet/gtest/*" \
                      "$(abs_builddir)/src/wallet/test/*" \
                      -o $@
else
block_test_filtered.info: block_test.info
	$(LCOV) -r $< "/usr/include/*" \
                      "$(abs_builddir)/depends/x86_64-unknown-linux-gnu/include/*.h" \
                      "$(abs_builddir)/depends/x86_64-unknown-linux-gnu/include/boost/*" \
                      "$(abs_builddir)/depends/x86_64-unknown-linux-gnu/include/gmock/*" \
                      "$(abs_builddir)/depends/x86_64-unknown-linux-gnu/include/gtest/*" \
                      "$(abs_builddir)/src/gtest/*" \
                      "$(abs_builddir)/src/test/*" \
                      "$(abs_builddir)/src/wallet/gtest/*" \
                      "$(abs_builddir)/src/wallet/test/*" \
                      -o $@
endif

test_komodo_coverage.info: baseline_filtered_combined.info test_komodo_filtered.info
       $(LCOV) -a baseline_filtered.info -a leveldb_baseline_filtered.info -a test_komodo_filtered.info -o $@

if ! BUILD_DARWIN
zcash-gtest_coverage.info: baseline_filtered_combined.info zcash-gtest_filtered.info
	$(LCOV) -a baseline_filtered.info -a leveldb_baseline_filtered.info -a zcash-gtest_filtered.info -o $@
endif

if BUILD_DARWIN
total_coverage.info:  baseline_filtered_combined.info test_komodo_filtered.info zcash-gtest_filtered.info block_test_filtered.info
  $(LCOV) -a baseline_filtered.info -a leveldb_baseline_filtered.info -a test_komodo_filtered.info -a zcash-gtest_filtered.info -a block_test_filtered.info -o $@ | $(GREP) "\%" | $(AWK) '{ print substr($$3,2,50) "/" $$5 }' > coverage_percent.txt
else
total_coverage.info:  baseline_filtered_combined.info test_komodo_filtered.info zcash-gtest_filtered.info block_test_filtered.info
	$(LCOV) -a baseline_filtered.info -a leveldb_baseline_filtered.info -a test_komodo_filtered.info -a zcash-gtest_filtered.info -a block_test_filtered.info -o $@ | $(GREP) "\%" | $(AWK) '{ print substr($$3,2,50) "/" $$5 }' > coverage_percent.txt
endif


test_komodo.coverage/.dirstamp:  test_komodo_coverage.info
       $(GENHTML) -s $< -o $(@D)
       @touch $@

if TARGET_DARWIN
zcash-gtest.coverage/.dirstamp:  zcash-gtest_coverage.info
  $(GENHTML) -s $< -o $(@D)
  @touch $@
cov-zcash: zcash-gtest.coverage/.dirstamp
endif

total.coverage/.dirstamp: total_coverage.info
       $(GENHTML) -s $< -o $(@D)
       @touch $@

if BUILD_DARWIN
cov: test_komodo.coverage/.dirstamp cov-zcash total.coverage/.dirstamp
else
cov: test_komodo.coverage/.dirstamp cov-zcash total.coverage/.dirstamp
endif

endif

if USE_COMPARISON_TOOL
check-local:
	$(MKDIR_P) qa/tmp
	@qa/pull-tester/run-komodod-for-test.sh $(JAVA) -jar $(JAVA_COMPARISON_TOOL) qa/tmp/compTool $(COMPARISON_TOOL_REORG_TESTS) 2>&1
endif

dist_bin_SCRIPTS = zcutil/fetch-params.sh
dist_noinst_SCRIPTS = autogen.sh zcutil/build-debian-package.sh zcutil/build.sh

EXTRA_DIST = $(top_srcdir)/share/genbuild.sh qa/pull-tester/rpc-tests.sh qa/pull-tester/run-komodo-cli qa/rpc-tests qa/zcash $(DIST_DOCS) $(BIN_CHECKS)

install-exec-hook:
	mv $(DESTDIR)$(bindir)/fetch-params.sh $(DESTDIR)$(bindir)/zcash-fetch-params

CLEANFILES = $(OSX_DMG) $(KOMODO_WIN_INSTALLER)

.INTERMEDIATE: $(COVERAGE_INFO)

DISTCHECK_CONFIGURE_FLAGS = --enable-man

clean-local:
	rm -rf test_komodo.coverage/ zcash-gtest.coverage/ total.coverage/ $(OSX_APP)
	rm -rf dist/ dpi36.background.tiff dpi72.background.tiff

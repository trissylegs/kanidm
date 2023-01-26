



VERSION := 1.1.0-alpha.9

APP_BASE := Kanidm
APP_NAME := $(APP_BASE).app
DMG_BASE := $(APP_BASE)-$(VERSION)
DMG := $(DMG_BASE).dmg

CARGO ?= $(HOME)/.cargo/bin/cargo

TOPLEVEL := ..

ifeq ($(CONFIGURATION),"Release")
CARGO_FLAGS:="--release"
TARGET_BIN_DIR="$(TOPLEVEL)/target/release"
else
TARGET_BIN_DIR="$(TOPLEVEL)/target/debug"
endif


DEV_CERT_ID:="PUT_YOUR_DEV_CERT_ID_HERE"

CODESIGN := /usr/bin/codesign --verbose --force --sign $(DEV_CERT_ID) --timestamp --preserve-metadata\=identifier,entitlements,flags --generate-entitlement-der
CODESIGN_BIN := $(CODESIGN) -o runtime

TIMESTAMP=$(shell date "+%Y%m%d_%H%M%S")

APP_DIR=./Kanidm.app

APP_TOP_DIR=$(APP_DIR)/Contents
APP_BIN_DIR=$(APP_DIR)/Contents/MacOS
APP_DOC_DIR=$(APP_TOP_DIR)/Resources/Documentation
SRC_INFO_PLIST=Info.plist

NOTARY_KEYCHAIN_PROFILE?=AC_PASSWORD

LIBSSL_PATH := /opt/homebrew/opt/openssl@3/lib/libssl.3.dylib
LIBCRYPTO_PATH := /opt/homebrew/opt/openssl@3/lib/libcrypto.3.dylib

tools:
	cd $(TOPLEVEL)
	$(CARGO) build -p kanidm_tools --bin kanidm $(cargo_flags)

$(APP_DIR): tools
	rm -rf $(APP_DIR)
	mkdir $(APP_DIR)

	mkdir -p "$(APP_TOP_DIR)"
	cp -R Resources "$(APP_TOP_DIR)"
	cp PkgInfo "$(APP_TOP_DIR)"
	cp Info.plist "$(APP_TOP_DIR)"
	mkdir -p "$(APP_DOC_DIR)"

	cp "$(TOPLEVEL)/LICENSE.md" "$(APP_DOC_DIR)"
	$(CODESIGN) "$(APP_DOC_DIR)/LICENSE.md"

	mkdir -p "$(APP_BIN_DIR)"
	./cp-with-libs "$(TARGET_BIN_DIR)/kanidm" "$(APP_BIN_DIR)"
	$(CODESIGN_BIN) "$(APP_BIN_DIR)/kanidm"
	$(CODESIGN_BIN) "$(APP_BIN_DIR)/libssl.3.dylib"
	$(CODESIGN_BIN) "$(APP_BIN_DIR)/libcrypto.3.dylib"
	$(CODESIGN) "$(APP_DIR)"


.PHONY: dmg
dmg: $(DMG)

$(DMG): $(APP_DIR)
	rm -rf dmg_tmp
	mkdir dmg_tmp
	cp -r "$(APP_DIR)" dmg_tmp/Kanidm.app
	hdiutil makehybrid -hfs -hfs-volume-name "Kanidm" -hfs-openfolder dmg_tmp dmg_tmp -o $@

.PHONY: tools



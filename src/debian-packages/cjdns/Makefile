#
# Build and package cjdns
#

PACKAGE_NAME = cjdns
VERSION = 20.0
DEBIAN_REVISION = 1
MAINTAINER = Benedict Lau <benedict.lau@groundupworks.com>
SOURCE_URL = https://github.com/cjdelisle/cjdns

TAG = cjdns-v20
ARCHS = amd64 armhf
BUILD_DIR = /tmp/$(PACKAGE_NAME)
OUTPUT_DIR = /vagrant/output/debian-packages

# Package deb for a particular architecture
#
# $1 is the architecture
define package_deb_arch
	mkdir -p $(BUILD_DIR)/debian/$(1)
	cp -r debian $(BUILD_DIR)/debian/$(1)/    # Copy deb package files
	sed -i "s|__PACKAGE__|$(PACKAGE_NAME)|g" $(BUILD_DIR)/debian/$(1)/debian/DEBIAN/control    # Replace __PACKAGE__ macro in control file
        # Use GitHub release version as deb_rev if set
	if test -n "${GITHUB_RELEASE_VERSION}"; \
	    then deb_rev=${GITHUB_RELEASE_VERSION}; \
	    else deb_rev=$(DEBIAN_REVISION); \
	  fi; \
	  sed -i "s|__VERSION__|$(VERSION)-$${deb_rev}|g" $(BUILD_DIR)/debian/$(1)/debian/DEBIAN/control    # Replace __VERSION__ macro in control file
	sed -i "s|__ARCHITECTURE__|$(1)|g" $(BUILD_DIR)/debian/$(1)/debian/DEBIAN/control    # Replace __ARCHITECTURE__ macro in control file
	sed -i "s|__MAINTAINER__|$(MAINTAINER)|g" $(BUILD_DIR)/debian/$(1)/debian/DEBIAN/control    # Replace __MAINTAINER__ macro in control file
	sed -i "s|__HOMEPAGE__|$(SOURCE_URL)|g" $(BUILD_DIR)/debian/$(1)/debian/DEBIAN/control    # Replace __HOMEPAGE__ macro in control file
	sed -i "s|__PACKAGE__|$(PACKAGE_NAME)|g" $(BUILD_DIR)/debian/$(1)/debian/usr/share/doc/$(PACKAGE_NAME)/copyright    # Replace __PACKAGE__ macro in copyright file
	sed -i "s|__MAINTAINER__|$(MAINTAINER)|g" $(BUILD_DIR)/debian/$(1)/debian/usr/share/doc/$(PACKAGE_NAME)/copyright    # Replace __MAINTAINER__ macro in copyright file
	sed -i "s|__SOURCE__|$(SOURCE_URL)|g" $(BUILD_DIR)/debian/$(1)/debian/usr/share/doc/$(PACKAGE_NAME)/copyright    # Replace __SOURCE__ macro in copyright file
	cp $(BUILD_DIR)/bin/$(1)/cjdroute $(BUILD_DIR)/debian/$(1)/debian/usr/bin/    # Copy binary
	mkdir -p $(BUILD_DIR)/debian/$(1)/debian/lib/systemd/system    # Make directories for systemd service files
	cp $(BUILD_DIR)/src/contrib/systemd/* $(BUILD_DIR)/debian/$(1)/debian/lib/systemd/system/    # Copy systemd service files
        # Use GitHub release version as deb_rev if set
	cd $(BUILD_DIR)/debian/$(1); \
	  dpkg-deb --build debian; \
	  if test -n "${GITHUB_RELEASE_VERSION}"; \
	    then deb_rev=${GITHUB_RELEASE_VERSION}; \
	    else deb_rev=$(DEBIAN_REVISION); \
	  fi; \
	  cp debian.deb $(OUTPUT_DIR)/$(PACKAGE_NAME)_$(VERSION)-$${deb_rev}_$(1).deb
endef

package_deb: build_bin_amd64 build_bin_armhf
	$(foreach arch,$(ARCHS),$(call package_deb_arch,$(arch));)

build_bin_amd64: download_source
	mkdir -p $(BUILD_DIR)/bin/amd64
	cd $(BUILD_DIR)/src; \
	  ./clean; \
	  ./do; \
	  cp cjdroute $(BUILD_DIR)/bin/amd64/

build_bin_armhf: download_source
	mkdir -p $(BUILD_DIR)/bin/armhf
	cd $(BUILD_DIR)/src; \
	  ./clean; \
	  CROSS_COMPILE=arm-linux-gnueabihf- ./cross-do; \
	  cp cjdroute $(BUILD_DIR)/bin/armhf/

download_source: mkdir_build
	git clone https://github.com/cjdelisle/cjdns.git -b $(TAG) $(BUILD_DIR)/src

mkdir_build:
	sudo rm -rf $(BUILD_DIR)
	mkdir -p $(BUILD_DIR)
	mkdir -p $(OUTPUT_DIR)

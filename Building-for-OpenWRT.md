For basic information on how to compile Linknx, please refer to [this page](Compiling).

Here I'll briefly introduce the method to cross-compile Linknx for OpenWRT.

The main tool used to achieve this is the OpenWRT SDK. This SDK packages everything needed to create the famous ".ipk" packages to install on the target device.
For this example, I'll use the SDK version labelled "Kamikaze r7908" for Broadcom based devices (like WRT54G routers).
Let's first download and uncompress this SDK.
```
wget http://downloads.openwrt.org/snapshots/brcm-2.4/OpenWrt-SDK-brcm-2.4-for-Linux-i686.tar.bz2
tar -xvjf OpenWrt-SDK-brcm-2.4-for-Linux-i686.tar.bz2
cd OpenWrt-SDK-brcm-2.4-for-Linux-i686
```
In the SDK main directory (I'll call it OpenWrt-SDK-xxx for the rest of this example), there is a folder package. For each package you want to create, you'll have to create a sub-folder in OpenWrt-SDK-xxx/package with the same name as the package you want to create. And in this directory, create a Makefile explaining how to build it.
Some tarballs containing the Makefiles and patches I'm using can be found in http://ouaye.net/linknx/OpenWRT-Kamikaze-r7908/build/ , you just have to untar them in OpenWrt-SDK-xxx/package
Here are the details of how to create the Linknx package from scratch
```
cd package
mkdir linknx
cd linknx
mkdir patches
touch Makefile
```
The Makefile I use looks like this:
```
# $Id: Makefile 1146 2005-06-05 13:32:28Z nbd $

include $(TOPDIR)/rules.mk

PKG_NAME:=linknx
PKG_VERSION:=0.0.1.20
PKG_RELEASE:=1
PKG_MD5SUM:=3063643c5d200b863cf3a794c22325aa

PKG_SOURCE_URL:=http://garr.dl.sourceforge.net/sourceforge/linknx
PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.gz
PKG_CAT:=zcat

PKG_BUILD_DIR:=$(BUILD_DIR)/$(PKG_NAME)-$(PKG_VERSION)

include $(INCLUDE_DIR)/package.mk

define Package/linknx
  SECTION:=net
  CATEGORY:=Network
  TITLE:=KNX home automation platform
  URL:=http://www.ouaye.net/linknx
  DEPENDS:=pthsem uclibc++
endef

define Build/Configure
    $(call Build/Configure/Default,--without-pth-test --with-pth=$(STAGING_DIR),\
                        CXXFLAGS="$(TARGET_CFLAGS) -fno-builtin -nostdinc++" \
                        CPPFLAGS="-I$(STAGING_DIR)/usr/include -I$(STAGING_DIR)/include" \
                        LDFLAGS="-nodefaultlibs -L$(STAGING_DIR)/usr/lib -L$(STAGING_DIR)/lib -luClibc++ -lc -lm -lgcc")
endef

define Build/Compile
    $(call Build/Compile/Default)
endef

define Package/linknx/install
    mkdir -p $(1)/usr/bin
    $(CP) $(PKG_BUILD_DIR)/src/linknx $(1)/usr/bin/
endef

$(eval $(call BuildPackage,linknx))
```
If you modify the Makefile or add patches in the patches subdirectory, you have to increment "PKG_RELEASE" in the makefile.

You'll also need to create packages for the dependencies. (pthsem >= 2.0.4 ; uclibc++ >= 0.2.2 and optionally curl or libesmtp)

Once the Makefiles (and patches if needed) are ready, you just have to go back to OpenWrt-SDK-xxx directory and build everything
```
cd ../..
make V=99
```
The V=99 option makes it much more verbose so that if it fails, you can more easily figure out what happened.
Once the build is successfull, all the ".ipk" files should be available in OpenWrt-SDK-xxx/bin/packages

More info about building packages with OpenWrt-SDK can be found at http://downloads.openwrt.org/kamikaze/docs/openwrt.html#x1-380002.1.2 
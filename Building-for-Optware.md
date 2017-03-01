For basic information on how to compile Linknx, please refer to (Compiling).

Here I'll briefly introduce the method to cross-compile Linknx for Optware.

The Optware package system was originally created to accompany the Unslung firmware for the NSLU2 (originally the packages were also called Unslung packages). It has since been expanded to cover a variety of other platforms, including Linksys NSLU2, Synology DS101, Freecom FSG3, ... More info at http://www.nslu2-linux.org/wiki/Optware/

In this example, I'll explain how to create the linknx package for Synology DS101 platform.
First get a copy of optware
```
svn co http://svn.nslu2-linux.org/svnroot/optware/trunk optware
cd optware/
```
Then prepare the toolchain for DS101
```
make ds101-target
cd ds101
make directories
make ipkg-utils
make toolchain
```
Note: For some platforms like FSG3v4, the "make toolchain" command doesn't do all the job. You'll have to download and install the toolchain manually using the information in the platform makefile (for fsg3v4: platforms/toolchain-fsg3v4.mk )

Now we'll have to create a makefile for each new package to build explaining how to build it. These makefiles are named <package_name>.mk and placed in the "optware/make" directory.

The makefiles I use can be found in http://ouaye.net/linknx/optware-makefiles/ But if you want to create the linknx package makefile from scratch, go to optware directory and execute
```
make make/linknx.mk
```
It will create a template linknx.mk file in optware/make that you can customize to your needs
More details at http://www.nslu2-linux.org/wiki/Optware/AddAPackageToOptware

Once you have the makefiles, go to optware/ds101 directory and build the packages like this:
```
make pthsem-ipk
make eibd-ipk
make linknx-ipk
```
After a successfull build, the ".ipk" files are located in optware/ds101/builds

For the QNAP TS109 hardware, the optware toolchain causes a segmentation fault (for linknx and eibd) during initialization of libpth. For these platforms, it's possible to install debian instead of the original firmware and compile eibd and linknx directly on the device.

First you have to install Debian:
http://wiki.qnap.com/wiki/Debian_Installation_On_QNAP

Then, once you are logged in as root, the list of commands is:
```
apt-get install gcc
apt-get install g++
apt-get install make
mkdir linknx
cd linknx
wget http://downloads.sourceforge.net/sourceforge/bcusdk/pthsem_2.0.7.tar.gz
wget http://downloads.sourceforge.net/sourceforge/linknx/linknx-0.0.1.26.t...
wget http://downloads.sourceforge.net/sourceforge/bcusdk/bcusdk_0.0.4.tar.gz
tar -xzf pthsem_2.0.7.tar.gz
tar -xzf linknx-0.0.1.26.tar.gz
tar -xzf bcusdk_0.0.4.tar.gz

cd pthsem-2.0.7/
./configure
make
make test
make install
ldconfig
cd ..
cd linknx-0.0.1.26/
./configure --without-log4cpp --without-lua
make
make install
cd ..
cd bcusdk-0.0.4/
./configure --enable-onlyeibd --enable-eibnetiptunnel --enable-usb --
enable-eibnetipserver --enable-ft12
make
make install
cd ..
```

I didn't install libcurl, libesmtp and LUA but they should work the same way. 

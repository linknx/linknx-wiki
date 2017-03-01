To create rpm packages for your system's architecture, you have to download the corresponding .src.rpm files from http://ouaye.net/linknx/src-rpm/
Then you just have to
```
rpmbuild --rebuild package_name.src.rpm
```
This has been tested on OpenSuse 10.2 only but it should also work on other platforms.
The resulting i586 rpm packages for Suse 10.2 can be found at http://ouaye.net/linknx/rpm-i586-suse-10.2/

For Linknx, you can also build the RPM directly from the source tarball with
```
rpmbuild -ta linknx-0.0.1.21.tar.gz
```
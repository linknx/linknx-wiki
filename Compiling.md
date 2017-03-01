Here's a description of how to compile Linknx from sources.

# Required libraries
* libpthsem >= 2.0.4 from http://sourceforge.net/projects/bcusdk

# Optional libraries
* libesmtp 1.0.4 from http://www.stafford.uklinux.net/libesmtp (needed for e-mail sending via SMTP)
* libcurl >= 7.14.0 from http://curl.haxx.se/download/ (needed for SMS sending using www.clickatell.com or Free Mobile API)
* cppunit >= 1.9.6 from http://sourceforge.net/projects/cppunit (needed for regression tests)
* log4cpp from http://log4cpp.sourceforge.net/ (Logging library allowing flexible logging configuration)
* lua >= 5.1 from http://www.lua.org/ (scripting for linknx conditions and actions) If you are installing libraries from RPM or any other package management tool, don't forget to install also "xxx-devel" packages

Download the source code (e.g. linknx-0.0.1.28.tar.gz) and unpack it:
```
wget http://sunet.dl.sourceforge.net/project/linknx/linknx/linknx-0.0.1.28/linknx-0.0.1.28.tar.gz
tar -xzf linknx-0.0.1.28.tar.gz
cd linknx-0.0.1.28
```

Configure
```
./configure
```
Build
```
make
```
Install
```
make install
```
Run regression tests (e.g. if you made some modifications to the code)
```
make check
```

# Notes

1. to activate optional features, specify them at configure time, like `./configure --with-log4cpp --with-lua --with-mysql=/usr/bin/mysql_config -enable-smtp`
2. full list of additional features and options can be obtained by typing `./configure --help=short`
3. some features require installation of additional libraries before compilation: `--with-mysql=/path/to/mysql_config` the libmysqlclient-dev library has to be installed before compilation and correct path to mysql_config file specified.

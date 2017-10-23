This page contains definition of a logging section in linknx's [configuration](Configuration).

# Logging section

Linknx supports two logging back-ends:
- Internal logger
- log4cpp external library

One of them is chosen at compile time. By default, the configure script will auto-detect the presence of the log4cpp library and use it if available. You can force log4cpp by passing --with-log4cpp option to configure script or disable it with option --without-log4cpp.

With Internal logger, the following parameters are available:
```xml
<logging format="simple" level="WARN"/>
```

If parameter format is omitted or if its value is different from "simple", the timestamp will be added in front of each log line.
The parameter level determines the minimum level of logging. The valid values are "DEBUG", "INFO", "NOTICE", "WARN" and "ERROR". The default value is "INFO". The level "DEBUG" is disabled in normal builds when using the internal logger.
The log messages for level ERROR or WARN go to stderr, all other messages go to stdout.

With log4cpp logger, the logging configuration looks like this:

```xml
<logging output="/var/log/linknx.log" format="%d{%Y%m%d %H:%M:%S,%l} %5p %c %x: %m%n" level="INFO"/>
```

The allowed parameters are:
- output: the log file to write. Default=stdout
- format: the logging format value can be "simple", "basic" or a pattern like in the example above.
- level: the minimum level of logging. The valid values are "DEBUG", "INFO", "NOTICE", "WARN" and "ERROR". Default=INFO
- maxfilesize: maximum size of a log file (in kb). When the size is reached, the logs are rotated
- maxfileindex: maximum index of log files kept. Default=10

The format pattern syntax allows the following fields:
* %%%% - a single percent sign
* %c - the category
* %d - the date > Date format: The date format character may be followed by a date format specifier enclosed between braces. For example, %d{%H:%M:%S,%l} or %d{%d %m %Y %H:%M:%S,%l}. If no date format specifier is given then the following format is used: "Wed Jan 02 02:03:55 1980". The date format specifier admits the same syntax as the ANSI C function strftime, with 1 addition. The addition is the specifier %l for milliseconds, padded with zeros to make 3 digits.
* %m - the message
* %n - the platform specific line separator
* %p - the priority
* %r - milliseconds since this layout was created.
* %R - seconds since Jan 1, 1970
* %u - clock ticks since process start
* %x - the NDC ||

format="simple" corresponds to "%p - %m%n" and format="basic" to "%R %p %c %x: %m%n".
The default value (if format is not specified or empty) is "%d [%5p] %c: %m%n"
More details about pattern layout can be found in log4j documentation but some of them are not supported by log4cpp

If parameter "config" is used, the value of that parameter is used as the name of the file containing log4cpp configuration. All other parameters are ignored. A sample configuration file that logs all warning and errors to a file and everything to the console can be found in linknx/conf/logging.conf (See log4cpp configuration for more details):
```xml
<logging config="/var/lib/linknx/logging.conf"/>
```


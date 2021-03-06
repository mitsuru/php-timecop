# php-timecop

[![Build Status](https://travis-ci.org/hnw/php-timecop.png)](https://travis-ci.org/hnw/php-timecop)

## DESCRIPTION

A PHP extension providing "time travel" and "time freezing" capabilities, inspired by [ruby timecop gem](https://github.com/travisjeffery/timecop).

## INSTALL

```
git clone https://github.com/hnw/php-timecop.git
cd php-timecop
phpize
./configure
make
make install
```

After install, add these lines to your php.ini

```ini
extension=timecop.so
```

## SYSTEM REQUIREMENTS

- OS: Linux, FreeBSD, MacOSX
- PHP: 5.2.x, 5.3.x, 5.4.x, 5.5.x
  - Tested only on 5.2.17, 5.3.21, 5.4.11, and 5.5.5
- SAPI: Apache, CLI
  - Other SAPIs are not tested, but there is no SAPI-dependent code.
- non-ZTS(recommended), ZTS

## FEATURES

- Freeze time to a specific point.
- Travel back to a specific point in time, but allow time to continue moving forward from there.
- Override the following PHP stock functions and class, which supports freezing or traveling time.
  - `time()`
  - `mktime()`
  - `gmmktime()`
  - `date()`
  - `gmdate()`
  - `idate()`
  - `getdate()`
  - `localtime()`
  - `strtotime()`
  - `strftime()`
  - `gmstrftime()`
  - `unixtojd()`
  - `DateTime::_construct()`
  - `DateTime::createFromFormat()` (PHP >= 5.3.4)
  - `date_create()`
  - `date_create_from_format()` (PHP >= 5.3.4)
- Rewrite value of the following global variables when the time has been moved.
  - `$_SERVER['REQUEST_TIME']`

## USAGE

```php
<?php
var_dump(date("Y-m-d")); // todays date
timecop_freeze(0);
var_dump(gmdate("Y-m-d H:i:s")); // string(19) "1970-01-01 00:00:00"
var_dump(strtotime("+100000 sec")); // int(100000)
```

### The difference between `timecop_freeze()` and `timecop_travel()`

`timecop_freeze()` is used to statically mock the concept of now. As your program executes, `time()` will not change unless you make subsequent calls into the Timecop API. `timecop_travel()`, on the other hand, computes an offset between what we currently think time() is and the time passed in. It uses this offset to simulate the passage of time. To demonstrate, consider the following code snippets:

```php
<?php
$new_time = mktime(12, 0, 0, 9, 1, 2008);
timecop_freeze($new_time);
sleep(10);
var_dump($new_time == time()); // bool(true)

timecop_return(); // "turn off" php-timecop

timecop_travel($new_time);
sleep(10);
var_dump($new_time == time()); // bool(false)
```

## CHANGELOG

###version 1.0.5, 2013/11/26
- Fix `TimecopDateTime::createFromFormat()` to reutrn `TimecopDateTime` instance on PHP >= 5.3.4
  - The previous version returns `DateTime` instance
  - Implement identical function `timecop_date_create_from_format()` 
  - BUG: not supporting "relative formats" for this method currently.
- Fix behavior of `TimecopDateTime::_construct()` when its 2nd argument is specified.

###version 1.0.4, 2013/03/11
- Fix SIGSEGV in `TimecopDateTime::__construct()` called with NULL as 1st argument

###version 1.0.3, 2013/03/09

- Fix the time traveling implementation for `TimecopDateTime::__construct()`
- Fix `timecop_date_create()` to return `TimecopDateTime` instance
  - The previous version returns `DateTime` instance
- Add `TimecopDateTime::getTimestamp()`, `TimecopDateTime::setTimestamp()` only for PHP 5.2.x

###version 1.0.2, 2013/03/06

- Implement `timecop_date_create()`

###version 1.0.1, 2013/03/04

- Implement time traveling feature for `TimecopDateTime::__construct()` with 1 or 2 arguments
  - The previous version works correctly only for no arguments calling: "new TimecopDateTime()"

###version 1.0.0, 2012/11/21

- Fix memory leak

###version 0.0.1, 2012/06/19

- Initial Release

## LICENSE
#
The MIT License

Copyright (c) 2012-2013 Yoshio HANAWA

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.

# To Install it #

Pre-requisites:
* PHP 8.1 - there's all kinds of version shenanigans going on here that you don't want to get bogged down in.
* Working install of Composer
* pcov needs to be installed, I used PECL...

Dump the contents of this folder into `admin/tool/paratest`

Do the following:
```
cd admin/tool/paratest/lib/paratest
git submodule init
composer install
cd -

php admin/tool/paratest/cli/init.php

php admin/tool/phpunit/cli/util.php --buildcomponentconfigs
```
Add the following to config.php, immediately before:
`require_once(dirname(__FILE__) . '/lib/setup.php');`

e.g.:
```
require_once(dirname(__FILE__) . '/admin/tool/paratest/classes/local/lib.php');
$CFG->phpunit_paraunit_processes = 10;
\tool_paratest\local\lib::confighook();

require_once(dirname(__FILE__) . '/lib/setup.php');
```

You can now run phpunit config files in parallel. Enjoy!

`./admin/tool/paratest/lib/paratest/bin/paratest --processes=10 --verbose --configuration /Users/andrewhancox/www/moodle45raw/mod/assign/phpunit.xml`

# Caveats #

The wrapper runner option - which would be a performance boost for Moodle - does not work, this is a known issuse:
```
Constants, static methods, static variables and everything exposed by test classes consumed by other test
classes (including Reflection) are not supported. This is due to a limitation of the current implementation of
WrapperRunner and how PHPUnit searches for classes. The fix is to put shared code into classes which are
not tests themselves.
```
In the latest version of paratest (v7) the wrapper runner option appears to be fixed which is currently a blocker to upgrading.

For this reason, the benefit of using this tool does not kick in until you are running a reasonably large number of tests across
a reasonably large number of processes (i.e. 10s). It's worth racking up the process count until your tests are constrained by
either CPU resources, hitting one process per test file, or the DB becomes a bottleneck.

On my machine, running 8 threads, the tests for the message subssytem complete in 2:42 when 8 processes are available, rather
than 6:20 - so an 8 fold increase in processes results in a 3 fold increase in throughput.

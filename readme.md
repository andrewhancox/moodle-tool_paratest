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

`./admin/tool/paratest/lib/paratest/bin/paratest --processes=10 --verbose --runner WrapperRunner --configuration /Users/andrewhancox/www/moodle45raw/mod/assign/phpunit.xml`

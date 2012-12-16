php-gpio
========


**php-gpio** is a simple PHP library to play with the Raspberry PI's GPIO pins.

It provides simple tools such as reading & writing to pins.

General Purpose Input/Output (a.k.a. GPIO) is a generic pin on a chip whose behavior
(including whether it is an input or output pin) can be controlled (programmed) through software.
The Raspberry Pi allows peripherals and expansion boards (such as the Rpi Gertboard)
to access the CPU by exposing the inputs and outputs.

For further informations about the Raspberry Pi's GPIO capabilities, see docs & schemas at http://elinux.org/RPi_Low-level_peripherals.

[![Build Status](https://secure.travis-ci.org/ronanguilloux/php-gpio.png?branch=master)](http://travis-ci.org/ronanguilloux/php-gpio)


Installation
------------

The recommended way to install php-gpio is through [composer](http://getcomposer.org).

Just create a `composer.json` file for your project:

``` json
{
     "require": {
        "php": ">=5.3.0",
        "ronanguilloux/php-gpio": "master-dev"
    }
}
```

And run these two commands to install it:

``` bash
$ wget http://getcomposer.org/composer.phar
$ php composer.phar install
```

Now you can add the autoloader, and you will have access to the library:

``` php
<?php

require 'vendor/autoload.php';
```

If you don't use neither **Composer** nor a _ClassLoader_ in your application, just require the provided autoloader:

``` php
<?php

require_once 'src/autoload.php';
```


API Usage
---------

``` php
<?php

    # blinker

    require 'vendor/autoload.php';

    use PhpGpio\Gpio;

    echo "Setting up pin 17\n";
    $gpio = new GPIO();
    $gpio->setup(17, "out");

    echo "Turning on pin 17\n";
    $gpio->output(17, 1);

    echo "Sleeping!\n";
    sleep(3);

    echo "Turning off pin 17\n";
    $gpio->output(17, 0);

    echo "Unexporting all pins\n";
    $gpio->unexportAll();
```


Understanding I/O permissions
-----------------------------

Permissions make sense: it's bad practice to run Apache2 user as root.

In order to blink a led without exposing you Raspbery Pi to security issues,
we provide a simple linker php file, executable from the shell.
To run this blinker with sudo permissions but without password inputting,
just allow your `www-data` or your `pi` user to run the blinker script.

With the solution provided below, only one blinker script is needed to manage all the leds, and your webserver application needs only one php file to be specified in /etc/sudoers.

Edit your `/etc/sudoers` file:

``` bash
$ sudo visudo
```

Then add this two lines in your `/etc/sudoers` file :

``` bash
pi ALL=NOPASSWD: /path/to/blinker
www-data ALL=NOPASSWD: /path/to/blinker
```

Note that you could also have written more pathes:

``` bash
myLinuxLogin ALL=NOPASSWD: /path/to/blinker1.php, /path/to/blinker2.php, /path/to/blinker3.php
```

The blinker file provided is ready to use the API. Just create a blinkTester.php file that calls the blinker.

``` php
<?php

    # blinkTester.php
    $result = exec('sudo ./blinker 17 20000');
```

Test your blinker:

``` bash
$ php blinkTester.php
```


API Implementations
-------------------

Some php-gpio api examples / demo :

* [Temperature-Pi](https://github.com/ronanguilloux/temperature-pi), a simple php project reading & logging temperatures using a DS18B20 1-Wire digital temperature sensor & this php-gpio library.


Unit Tests
----------

Running the full PhpUnit tests set over php-gpio requires a sudoable user, because of various gpio operations.
Such practice isn't security-aware & therefore not recommeded in an Internet environment (see I/O permissions section).
Instead of installing phpunit, you can just download & use the single PhpUnit package.
This can be easily done using `cURL`, to get the standalone PhpUnit's phar file:

``` bash
$ wget http://pear.phpunit.de/get/phpunit.phar
$ chmod +x phpunit.phar
$ wget http://getcomposer.org/composer.phar
$ php composer.phar install --dev
$ sudo /usr/bin/php phpunit.phar
```


PHP Quality
-----------

For [PHP quality fans](http://phpqatools.org), and for my self coding improvement, I wrote a little script available in the ./bin directory I launch to check my PHP code: It produces various stats & metrics & improvements tips on the code.


Credits
-------

* Aaron Pearce, for its [forked pickley/PHP-GPIO project](https://github.com/pickley/PHP-GPIO)
* Ronan Guilloux <ronan.guilloux@gmail.com>
* [All contributors](https://github.com/ronanguilloux/php-gpio/contributors)


License
-------

**php-gpio** is released under the MIT License. See the bundled LICENSE file for details.
You can find a copy of this software here: https://github.com/ronanguilloux/php-gpio


============composer安装====================

需要openssl和zlib支持


[ssy@localhost testdir]$ wget https://getcomposer.org/installer

[ssy@localhost testdir]$ php installer
    Some settings on your machine make Composer unable to work properly.
    Make sure that you fix the issues listed below and run this script again:

    The openssl extension is missing, which means that secure HTTPS transfers are impossible.
    If possible you should enable it or recompile php with --with-openssl

[ssy@localhost testdir]$ 安装完了openssl...

[ssy@localhost testdir]$ php installer
    Downloading...

    Composer (version 1.4.2) successfully installed to: /home/ssy/testdir/composer.phar
    Use it: php composer.phar

    Some settings on your machine may cause stability issues with Composer.
    If you encounter issues, try to change the following:

    The zlib extension is not loaded, this can slow down Composer a lot.
    If possible, install it or recompile php with --with-zlib

    The php.ini used by your command-line PHP is: /usr/local/php7.1.5/etc/php.ini
    If you can not modify the ini file, you can also run `php -d option=value` to modify ini values on the fly. You can use -d multiple times.

[ssy@localhost ~]$ cd php-7.1.5/ext/zlib

[ssy@localhost openssl]$ mv config0.m4 config.m4

[ssy@localhost openssl]$ phpize

[ssy@localhost zlib]$ ./configure --with-zlib --with-php-config=/usr/local/php7.1.5/bin/php-config 
    config.status: creating config.h

[ssy@localhost openssl]$ make

[ssy@localhost openssl]$ sudo make install

[ssy@localhost zlib]$ php -i|grep zlib
    gzip compression => disabled (install ext/zlib)
    PWD => /home/ssy/php-7.1.5/ext/zlib
    $_SERVER['PWD'] => /home/ssy/php-7.1.5/ext/zlib

[ssy@localhost zlib]$ sudo vi /usr/local/php7.1.5/etc/php.ini
    ;extension = zlib.so

[ssy@localhost zlib]$ php -i|grep zlib
    Registered PHP Streams => php, file, glob, data, http, ftp, https, ftps, compress.zlib, phar
    Registered Stream Filters => convert.iconv.*, string.rot13, string.toupper, string.tolower, string.strip_tags, convert.*, consumed, dechunk, zlib.*
    zlib
    Stream Wrapper => compress.zlib://
    Stream Filter => zlib.inflate, zlib.deflate
    zlib.output_compression => Off => Off
    zlib.output_compression_level => -1 => -1
    zlib.output_handler => no value => no value
    PWD => /home/ssy/php-7.1.5/ext/zlib
    $_SERVER['PWD'] => /home/ssy/php-7.1.5/ext/zlib

[ssy@localhost testdir]$ php installer --filename=composer
    All settings correct for using Composer
    Downloading...

    Composer (version 1.4.2) successfully installed to: /home/ssy/testdir/composer
    Use it: php composer







Ansible Role: PHP
=========
[![Build Status](https://travis-ci.org/1nfinitum/ansible-php.svg?branch=master)](https://travis-ci.org/1nfinitum/ansible-php)

Installs PHP and accomplishes its configurations on Ubuntu Xenial.

Role Variables
--------------

Available variables are listed below, along with default values (see `defaults/main.yml`):

    php_packages: []

A list of the PHP packages to install. You'll likely want to install common packages like `php`, `php-cli`, `php-devel` and `php-pdo`, and you can add in whatever other packages you'd like (for example, `php-gd` for image manipulation, or `php-ldap` if you need to connect to an LDAP server for authentication).

    php_packages_extra: []

A list of extra PHP packages to install without overriding the default list.

    php_enable_webserver: true

If your usage of PHP is tied to a web server (e.g. Apache or Nginx), leave this default value. If you are using PHP server-side or to run some small application, set this value to `false` so this role doesn't attempt to interact with a web server.

    php_webserver_daemon: "nginx"

The default values for the HTTP server daemon is `apache2` (used by Apache) for Debian/Ubuntu. If you are running another webserver (for example, `nginx`), change this value to the name of the daemon under which the webserver runs.

### PHP-FPM settings

PHP-FPM is a simple and robust FastCGI Process Manager for PHP. It can dramatically ease scaling of PHP apps and is the normal way of running PHP-based sites and apps when using a webserver like Nginx (though it can be used with other webservers just as easily).

When using this role with PHP running as `php-fpm` instead of as a process inside a webserver (e.g. Apache's `mod_php`), you need to set the following variable to `true`:

    php_enable_php_fpm: false

If you're using Apache, you can easily get it configured to work with PHP-FPM using the [geerlingguy.apache-php-fpm](https://github.com/geerlingguy/ansible-role-apache-php-fpm) role.

    php_fpm_listen: "127.0.0.1:9000"
    php_fpm_listen_allowed_clients: "127.0.0.1"
    php_fpm_pm_max_children: 50
    php_fpm_pm_start_servers: 5
    php_fpm_pm_min_spare_servers: 5
    php_fpm_pm_max_spare_servers: 5

Specific settings inside the default `www.conf` PHP-FPM pool. If you'd like to manage additional settings, you can do so either by replacing the file with your own template or using `lineinfile` like this role does inside `tasks/configure-fpm.yml`.

### php.ini settings

    php_use_managed_ini: true

By default, all the extra defaults below are applied through the php.ini included with this role. You can self-manage your php.ini file (if you need more flexility in its configuration) by setting this to `false` (in which case all the below variables will be ignored).

    php_memory_limit: "256M"
    php_max_execution_time: "60"
    php_max_input_time: "60"
    php_max_input_vars: "1000"
    php_realpath_cache_size: "32K"
    php_file_uploads: "On"
    php_upload_max_filesize: "64M"
    php_max_file_uploads: "20"
    php_post_max_size: "32M"
    php_date_timezone: "America/Chicago"
    php_allow_url_fopen: "On"
    php_sendmail_path: "/usr/sbin/sendmail -t -i"
    php_output_buffering: "4096"
    php_short_open_tag: false
    php_error_reporting: "E_ALL & ~E_DEPRECATED & ~E_STRICT"
    php_display_errors: "Off"
    php_display_startup_errors: "On"
    php_expose_php: "On"
    php_session_cookie_lifetime: 0
    php_session_gc_probability: 1
    php_session_gc_divisor: 1000
    php_session_gc_maxlifetime: 1440
    php_session_save_handler: files
    php_session_save_path: ''
    php_disable_functions: []

Various defaults for PHP. Only used if `php_use_managed_ini` is set to `true`.

### OpCache-related settings

    php_opcache_zend_extension: "opcache.so"
    php_opcache_enable: "1"
    php_opcache_enable_cli: "0"
    php_opcache_memory_consumption: "96"
    php_opcache_interned_strings_buffer: "16"
    php_opcache_max_accelerated_files: "4096"
    php_opcache_max_wasted_percentage: "5"
    php_opcache_validate_timestamps: "1"
    php_opcache_revalidate_path: "0"
    php_opcache_revalidate_freq: "2"
    php_opcache_max_file_size: "0"

OpCache ini directives that are often customized on a system. Make sure you have enough memory and file slots allocated in the OpCache (`php_opcache_memory_consumption`, in MB, and `php_opcache_max_accelerated_files`) to contain all the PHP code you are running. If not, you may get less-than-optimal performance!

For custom opcache.so location provide full path with `php_opcache_zend_extension`.

Example Playbook
----------------

    - hosts: all
      vars_files:
        - vars/main.yml
      roles:
         - 1nfinitum.php

*Inside `vars/main.yml`*:

    php_memory_limit: "128M"
    php_max_execution_time: "90"
    php_upload_max_filesize: "256M"
    php_packages:
      - php
      - php-cli
      - php-common
      - php-devel
      - php-gd
      - php-mbstring
      - php-pdo
      - php-pecl-apcu
      - php-xml
      ...

License
-------

MIT

Author Information
------------------

This role was created in 2017 by [Mykhaylo Kolesnik](http://github.com/1nfinitum).

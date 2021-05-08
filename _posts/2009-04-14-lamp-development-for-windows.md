---
layout: post
title: "LAMP Development for Windows"
date: 2009-04-14 13:10 -0400
tags: lamp windows
---

I have been doing development in PHP for a long time and have yet to find a really satisfactory solution on Windows.  When I recently flattened my home PC and had to restart I thought I would try a new setup and here is what I’m using.  Its pretty good to me so far, and I think I’m going to run with it for the time being:

### IDE

For an IDE I am using [EasyEclipse for LAMP](http://www.easyeclipse.org/site/distributions/lamp.html) which is a nice little package.  It uses Eclipse as its base IDE but integrates plugins for PHP, Python, Ruby on Rails (not that good), Subclipse and others.  Just download the latest build and install it.

### XAMPP

To run locally you can install [WAMP](http://www.wampserver.com/en/) (I used to use this, it was good) or [XAMPP](http://www.apachefriends.org/en/xampp.html).  I am trying XAMPP for now because it is integrated nicely into Eclipse.  Out of the box you will get MySQL, Apache, FileZilla and Mercury up and running if you install this.


At this point you can import directly from you subversion repository into the xampp\htdocs directory and you should have a working enlistment deployed (make sure Apache is started) to `http://localhost/your_enlistment`


There are a few more things that you might want to install, including PEAR and Memcached.

### PEAR

To install [PEAR](http://pear.php.net/) for XAMPP, open a command prompt (cmd.exe) as an Administrator (Vista only).


Then navigate the your `xampp\php\PEAR\PEAR` and execute the go-pear.phar script (its PHP) via:


```bash
php go-pear.phar
```

You should be able to use the default settings (unless you’ve tweaked your XAMPP installation).  When prompted as to whether you would like to update your php.ini file, say Yes.


At this point you should be done and can now reference PEAR libraries in your PHP scripts.

### Memcached

If you need [memcached](http://www.danga.com/memcached/) you can get this up and running in a variety of ways.  If you want to build from source you can find it [here](http://www.danga.com/memcached/download.bml).  Otherwise search for a [pre-compiled windows32 version](http://www.splinedancer.com/memcached-win32/).  Copy the binaries to some place on your development machine and then you will need to start and install the service.


To install the service open a command prompt as an administrator and navigate to the directory with memcached.exe.  Install the service by typing: `memcached -d install`


To start the service type: `memcached -d start`


Note that the default reserved cache size for memcached is only 64MB, so if you would like it to be larger, launch the service and append a -m followed by the number of megabytes as an integer.  So to launch and reserve a half gig you would launch it as: `memcached -d start -m 512`


Now that the service is running, you will need to let php know that it is.  Make sure that you have the correct extension (php_memcache.dll) in the `php\ext` folder in your xampp directory.  After you have verified that this is there, you should update the php.ini file and uncomment (remove the preceding “;”) the following line:

```ini
extension=php_memcache.dll
```

Make sure that this is the php.ini file associated with apache if you want to test this on your local pc (i.e. the one in your apache folder), otherwise you will just have access to this via the CLI version of PHP.  Restart Apache and you should be good to go.  If you are having issues make sure that there is a Memcache section in your phpinfo() output to verify that the extension is being loaded.


Hope this helps, leave any questions in the comments and I’ll try to help.
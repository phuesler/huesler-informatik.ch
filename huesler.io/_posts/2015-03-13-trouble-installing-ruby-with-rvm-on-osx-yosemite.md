---
layout: post
title: "Problems installing Ruby with RVM on OSX Yosemite 10.10.2"
---

I tried to install [Ruby 1.9.3](https://www.ruby-lang.org/en/downloads/) using [RVM](https://rvm.io) but it had problems auto detecting the correct compiler.

# TL;DR

## Error

{% highlight bash %}
$: rvm install 1.9.3
...
couldn't understand kern.osversion `14.1.0'
ld: dylib ./libruby.1.9.1.dylib missing LC_ID_DYLIB load command file './libruby.1.9.1.dylib' for architecture x86_64
collect2: ld returned 1 exit status
make[1]: *** [.ext/x86_64-darwin14.1.0/enc/encdb.bundle] Error 1
make: *** [enc] Error 2
+__rvm_make:0> return 2
There has been an error while running make. Halting the installation.
{% endhighlight %}

## Solution

{% highlight bash %}
$: brew install gcc48
$: CC=/usr/local/Cellar/gcc48/4.8.4/bin/gcc-4.8 rvm install 1.9.3
{% endhighlight %}


# Background information

{% highlight bash %}
$: rvm install 1.9.3
Searching for binary rubies, this might take some time.
No binary rubies available for: osx/10.10/x86_64/ruby-1.9.3-p551.
Continuing with compilation. Please read 'rvm help mount' to get more information on binary rubies.
Checking requirements for osx.
Certificates in '/usr/local/etc/openssl/cert.pem' are already up to date.
Requirements installation successful.
Installing Ruby from source to: /Users/phuesler/.rvm/rubies/ruby-1.9.3-p551, this may take a while depending on your cpu(s)...
ruby-1.9.3-p551 - #downloading ruby-1.9.3-p551, this may take a while depending on your connection...
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100 9813k  100 9813k    0     0  2669k      0  0:00:03  0:00:03 --:--:-- 2669k
ruby-1.9.3-p551 - #extracting ruby-1.9.3-p551 to /Users/phuesler/.rvm/src/ruby-1.9.3-p551 - please wait
ruby-1.9.3-p551 - #applying patch /Users/phuesler/.rvm/patches/ruby/GH-488.patch - please wait
ruby-1.9.3-p551 - #configuring - please wait
ruby-1.9.3-p551 - #post-configuration - please wait
ruby-1.9.3-p551 - #compiling - please wait
Error running '__rvm_make -j 1',
showing last 15 lines of /Users/phuesler/.rvm/log/1426179507_ruby-1.9.3-p551/make.log
linking static-library libruby.1.9.1-static.a
linking shared-library libruby.1.9.1.dylib
couldn't understand kern.osversion `14.1.0'
generating encdb.h
encdb.h unchanged
making enc
compiling ./enc/encdb.c
couldn't understand kern.osversion `14.1.0'
linking encoding encdb.bundle
couldn't understand kern.osversion `14.1.0'
ld: dylib ./libruby.1.9.1.dylib missing LC_ID_DYLIB load command file './libruby.1.9.1.dylib' for architecture x86_64
collect2: ld returned 1 exit status
make[1]: *** [.ext/x86_64-darwin14.1.0/enc/encdb.bundle] Error 1
make: *** [enc] Error 2
+__rvm_make:0> return 2
There has been an error while running make. Halting the installation.
{% endhighlight %}


Looking at the error log showed that it might be using an incompatible version of [gcc](https://gcc.gnu.org).


{% highlight bash %}
$: head /Users/phuesler/.rvm/log/1426179507_ruby-1.9.3-p551/make.log
+__rvm_make:0> make -j 1
	CC = /usr/local/opt/apple-gcc42/bin/gcc-4.2
	LD = ld
	LDSHARED = /usr/local/opt/apple-gcc42/bin/gcc-4.2 -dynamiclib
	CFLAGS = -O3 -g   -fno-common -pipe
	XCFLAGS = -include ruby/config.h -include ruby/missing.h -DRUBY_EXPORT
	CPPFLAGS = -D_XOPEN_SOURCE -D_DARWIN_C_SOURCE   -I/usr/local/opt/libyaml/include -I/usr/local/opt/readline/include -I/usr/local/opt/libksba/include -I/usr/local/opt/openssl/include -I. -I.ext/include/x86_64-darwin14.1.0 -I./include -I.
	DLDFLAGS = -Wl,-undefined,dynamic_lookup -Wl,-multiply_defined,suppress -Wl,-flat_namespace -install_name /Users/phuesler/.rvm/rubies/ruby-1.9.3-p551/lib/libruby.1.9.1.dylib -current_version 1.9.1 -compatibility_version 1.9.1  -Wl,-u,_objc_msgSend
	SOLIBS =
compiling main.c
{% endhighlight %}

So let's try to compile it using [apples clang](https://developer.apple.com/library/mac/documentation/CompilerTools/Conceptual/LLVMCompilerOverview/) which ships with [xcode](https://developer.apple.com/xcode/):


{% highlight bash %}
$:  /usr/bin/gcc -v
Configured with: --prefix=/Applications/Xcode.app/Contents/Developer/usr --with-gxx-include-dir=/usr/include/c++/4.2.1
Apple LLVM version 6.0 (clang-600.0.57) (based on LLVM 3.5svn)
Target: x86_64-apple-darwin14.1.0
Thread model: posix
{% endhighlight %}

{% highlight bash %}
CC=/usr/bin/gcc rvm reinstall 1.9.3
ruby-1.9.3-p551 - #removing src/ruby-1.9.3-p551 - please wait
ruby-1.9.3-p551 - #removing rubies/ruby-1.9.3-p551 - please wait
Searching for binary rubies, this might take some time.
No binary rubies available for: osx/10.10/x86_64/ruby-1.9.3-p551.
Continuing with compilation. Please read 'rvm help mount' to get more information on binary rubies.
Checking requirements for osx.
Certificates in '/usr/local/etc/openssl/cert.pem' are already up to date.
Requirements installation successful.
Warning: found user selected compiler '/usr/bin/gcc', this will suppress RVM auto detection mechanisms.
Installing Ruby from source to: /Users/phuesler/.rvm/rubies/ruby-1.9.3-p551, this may take a while depending on your cpu(s)...
ruby-1.9.3-p551 - #downloading ruby-1.9.3-p551, this may take a while depending on your connection...
ruby-1.9.3-p551 - #extracting ruby-1.9.3-p551 to /Users/phuesler/.rvm/src/ruby-1.9.3-p551 - please wait
ruby-1.9.3-p551 - #applying patch /Users/phuesler/.rvm/patches/ruby/GH-488.patch - please wait
ruby-1.9.3-p551 - #configuring - please wait
ruby-1.9.3-p551 - #post-configuration - please wait
ruby-1.9.3-p551 - #compiling - please wait
qruby-1.9.3-p551 - #installing - please wait
ruby-1.9.3-p551 - #making binaries executable - please wait
ruby-1.9.3-p551 - #downloading rubygems-2.4.6
ruby-1.9.3-p551 - #extracting rubygems-2.4.6 - please wait
ruby-1.9.3-p551 - #removing old rubygems - please wait
ruby-1.9.3-p551 - #installing rubygems-2.4.6 - please wait
ruby-1.9.3-p551 - #gemset created /Users/phuesler/.rvm/gems/ruby-1.9.3-p551@global
ruby-1.9.3-p551 - #importing gemset /Users/phuesler/.rvm/gemsets/global.gems - please wait
ruby-1.9.3-p551 - #generating global wrappers - please wait
ruby-1.9.3-p551 - #gemset created /Users/phuesler/.rvm/gems/ruby-1.9.3-p551
ruby-1.9.3-p551 - #importing gemsetfile /Users/phuesler/.rvm/gemsets/default.gems evaluated to empty gem list
ruby-1.9.3-p551 - #generating default wrappers - please wait
ruby-1.9.3-p551 - #adjusting #shebangs for (gem irb erb ri rdoc testrb rake).
Install of ruby-1.9.3-p551 - #complete
Ruby 'ruby-1.9.3-p551' was built using clang - but it's not (fully) supported, expect errors.
WARNING: Please be aware that you just installed a ruby that is no longer maintained (2014-02-23), for a list of maintained rubies visit:

    http://bugs.ruby-lang.org/projects/ruby/wiki/ReleaseEngineering

Please consider upgrading to ruby-2.2.0 which will have all of the latest security patches.
Ruby was built without documentation, to build it run: rvm docs generate-ri
{% endhighlight %}

Turns out building Ruby 1.9.3 with [clang](http://clang.llvm.org) is not fully supported:

<pre>
Ruby 'ruby-1.9.3-p551' was built using clang - but it's not (fully) supported, expect errors.
</pre>

Let's try the latest gcc we can find on homebrew:

{% highlight bash %}
$: brew install gcc48
==> Installing gcc48 from homebrew/homebrew-versions
==> Downloading https://downloads.sf.net/project/machomebrew/Bottles/versions/gcc48-4.8.4.yosemite.
Already downloaded: /Library/Caches/Homebrew/gcc48-4.8.4.yosemite.bottle.tar.gz
==> Pouring gcc48-4.8.4.yosemite.bottle.tar.gz
[brew]  /usr/local/Cellar/gcc48/4.8.4: 1028 files, 142M
{% endhighlight %}

{% highlight bash %}
$: CC=/usr/local/Cellar/gcc48/4.8.4/bin/gcc-4.8 rvm install 1.9.3
ruby-1.9.3-p551 - #removing src/ruby-1.9.3-p551 - please wait
ruby-1.9.3-p551 - #removing rubies/ruby-1.9.3-p551 - please wait
Searching for binary rubies, this might take some time.
No binary rubies available for: osx/10.10/x86_64/ruby-1.9.3-p551.
Continuing with compilation. Please read 'rvm help mount' to get more information on binary rubies.
Checking requirements for osx.
Certificates in '/usr/local/etc/openssl/cert.pem' are already up to date.
Requirements installation successful.
Warning: found user selected compiler '/usr/local/Cellar/gcc48/4.8.4/bin/gcc-4.8', this will suppress RVM auto detection mechanisms.
Installing Ruby from source to: /Users/phuesler/.rvm/rubies/ruby-1.9.3-p551, this may take a while depending on your cpu(s)...
ruby-1.9.3-p551 - #downloading ruby-1.9.3-p551, this may take a while depending on your connection...
ruby-1.9.3-p551 - #extracting ruby-1.9.3-p551 to /Users/phuesler/.rvm/src/ruby-1.9.3-p551 - please wait
ruby-1.9.3-p551 - #applying patch /Users/phuesler/.rvm/patches/ruby/GH-488.patch - please wait
ruby-1.9.3-p551 - #configuring - please wait
ruby-1.9.3-p551 - #post-configuration - please wait
ruby-1.9.3-p551 - #compiling - please wait
ruby-1.9.3-p551 - #installing - please wait
ruby-1.9.3-p551 - #making binaries executable - please wait
ruby-1.9.3-p551 - #downloading rubygems-2.4.6
ruby-1.9.3-p551 - #extracting rubygems-2.4.6 - please wait
ruby-1.9.3-p551 - #removing old rubygems - please wait
ruby-1.9.3-p551 - #installing rubygems-2.4.6 - please wait
ruby-1.9.3-p551 - #gemset created /Users/phuesler/.rvm/gems/ruby-1.9.3-p551@global
ruby-1.9.3-p551 - #importing gemset /Users/phuesler/.rvm/gemsets/global.gems - please wait
ruby-1.9.3-p551 - #generating global wrappers - please wait
ruby-1.9.3-p551 - #gemset created /Users/phuesler/.rvm/gems/ruby-1.9.3-p551
ruby-1.9.3-p551 - #importing gemsetfile /Users/phuesler/.rvm/gemsets/default.gems evaluated to empty gem list
ruby-1.9.3-p551 - #generating default wrappers - please wait
ruby-1.9.3-p551 - #adjusting #shebangs for (gem irb erb ri rdoc testrb rake).
Install of ruby-1.9.3-p551 - #complete
WARNING: Please be aware that you just installed a ruby that is no longer maintained (2014-02-23), for a list of maintained rubies visit:

    http://bugs.ruby-lang.org/projects/ruby/wiki/ReleaseEngineering

Please consider upgrading to ruby-2.2.0 which will have all of the latest security patches.
Ruby was built without documentation, to build it run: rvm docs generate-ri
{% endhighlight %}


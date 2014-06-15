---
layout: post
title: Compiling Vim on Osx Maverick
category: unix
tag: blog
---

I recently switch my linux machine for a mac. Of course, one of the first thing that I wanted to do was to install vim.

Since I want to use the latest version with the some features that aren't included by default, I need to compile it.
In my experience, compiling vim was always pretty easy and straight forward, however, I found out that it can be a little bit tricky on Osx Maverick.

Here is the way I could do it

First you need to download the latest release

      wget ftp://ftp.vim.org/pub/vim/unix/vim-7.4.tar.bz2

You then need to unpack it with a tar command

      tar xjf vim-7.4.tar.bz2

You then cd into the src directory

      cd vim74/src

You can now configure your vim installation, here is the configuration that I use.

{% highlight bash %}

    ./configure --with-features=huge --enable-rubyinterp --enable-pythoninterp --enable-gui=no --disable-gpm --enable-multibyte

{% endhighlight %}

Normaly after you could just run

{% highlight bash %}

    make && make install

{% endhighlight %}

Sadly, there is bug that prevent the compilation from working. I was getting this error relative to sigaltstack

{% highlight bash %}

    os_unix.c:830:46: warning: declaration of 'struct sigaltstack' will not be visible outside of this function [-Wvisibility]
        extern int sigaltstack __ARGS((const struct sigaltstack *ss, struct sigaltstack *oss));

{% endhighlight %}

I found the fix on a [mailing list](https://groups.google.com/d/msg/vim_dev/YHo51sA46fU/cybmVD3v3SkJ)
You need to perform the following change to the os_unix.c file

{% highlight c %}

    +--- a/src/os_unix.c  2013-06-13 16:50:33.000000000 +0200
    ++++ b/src/os_unix.c  2013-06-13 16:50:40.000000000 +0200
    +@@ -18,6 +18,10 @@
    + 
    ++#if defined(__APPLE__)
    ++#include <AvailabilityMacros.h>
    ++#endif

    diff -r c1170f618303 src/os_unix.c
    --- a/src/os_unix.c     Fri Jun 14 22:48:54 2013 +0200
    +++ b/src/os_unix.c     Sat Jun 15 09:04:55 2013 -0500
    @@ -827,7 +827,7 @@
                    || MAC_OS_X_VERSION_MAX_ALLOWED <= 1040)
            /* missing prototype.  Adding it to osdef?.h.in doesn't work, because
             * "struct sigaltstack" needs to be declared. */
    -       extern int sigaltstack __ARGS((const struct sigaltstack *ss, struct sigaltstack *oss));
    +    extern int sigaltstack __ARGS((const stack_t *restrict ss, stack_t *restrict oss));
     #  endif

{% endhighlight %}

You should then be able to compile vim without any issues, enjoy!
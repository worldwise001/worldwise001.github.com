---
author: worldwise001
layout: post
title: The Curious Case of Python, Catalina, Snowflake, and Abort Trap 6
description: Some time ago I had upgraded my work laptop from Mojave to Catalina. The result of that, as a lot of you probably know, is that due to the fact that it now only supports 64-bit applicatons this meant a lot of apps/libraries stopped working, complaining of mismatched dylibs.
image: /images/2020-01-04-python-abort-trap-6/abort-trap-6.png
---

![Python Quit Unexpectedly Dialog - Details](/images/2020-01-04-python-abort-trap-6/abort-trap-6.png)

Some time ago I had upgraded my work laptop from Mojave to Catalina. The result of that, as a lot of you probably know, is that due to the fact that it [now only supports 64-bit applicatons](https://en.wikipedia.org/wiki/MacOS_Catalina) this meant a lot of apps/libraries stopped working, complaining of mismatched dylibs. As a veteran Linux user, I had seen something similar many many times with mismatched .so's, and knew the standard approach was to just reinstall everything, because with an OS version upgrade, the standard Mac OS libraries probably changed versions/ABIs causing linkage errors/other inconsistencies. Typically that meant just doing `brew upgrade` and stepping out for a coffee.

### Python still complains

A few python things remained persistently problematic, which is why I had these scripts handy ready to blow out various parts of the environment:

```
#!/bin/bash

pushd ~/Library/Caches/pip && rm -rf http wheels && popd
pushd /usr/local/lib/python3.7/site-packages && rm -rf `find . -name __pycache__` && popd
```

```
#!/bin/sh

rm -rf dist/ *.egg-info/ build/ venv/ .pytest_cache/ .mypy_cache/
rm -rf `find . -name __pycache__`

python3 -m venv venv
source venv/bin/activate
pip install --upgrade pip
pip install -r requirements.txt
```

Despite all that, I still remained flummoxed by the fact that one service I was working on locally that was using [snowflake-connector-python](https://github.com/snowflakedb/snowflake-connector-python) was still quitting unexpectedly with `Abort trap: 6`.

### What is `Abort trap: 6`?

Mac uses [this definition](https://en.wikipedia.org/wiki/Trap_%28computing%29) of trap; this message appears when the `abort()` function in the C standard library is called. It is akin to [SIGABRT](https://en.wikipedia.org/wiki/Signal_%28IPC%29). There are [various reasons](https://en.wikipedia.org/wiki/Abort_%28computing%29) to do an `abort()` over doing a standard application shutdown, but typically it's more controlled than a segfault, but deeper into library/system internals and thus out of control of the developer. An `abort()` in the Linux kernel for instance will cause a kernel panic.

### Getting Detailed Information

Weirdly enough, when `abort()` is called in an application in Mac OS X, a very helpful dialog titled "$program quit unexpectedly" pops up, and it's only through there that you can see detailed error messages, despite the fact that the application may have been called on the command line.

![Python Quit Unexpectedly Dialog](/images/2020-01-04-python-abort-trap-6/python-quit-unexpectedly.png)

Either way, clicking on "Report" allowed me to dig in more into the messages to see what was the problem.

```
Crashed Thread:        2

Exception Type:        EXC_CRASH (SIGABRT)
Exception Codes:       0x0000000000000000, 0x0000000000000000
Exception Note:        EXC_CORPSE_NOTIFY

Application Specific Information:
/usr/lib/libcrypto.dylib
abort() called
Invalid dylib load. Clients should not load the unversioned libcrypto dylib as it does not have a stable ABI.
```

Huh. That's odd.

### Initial Googling

It turns out that I wasn't the only one encountering this error; [many other folks](https://forums.developer.apple.com/thread/119429) had encountered this, and despite being shown the [proper fix](https://forums.developer.apple.com/thread/119429#380112), some folks had posted a [workaround](https://forums.developer.apple.com/thread/119429#386843) that consisted of overwriting the library with a symlink to a pinned version. I decided that ultimately that was pretty tacky and not a good long-term solution, since inevitably there was no consistent version to link to, folks would eventually run into the problem again.

I went to go report this as a bug to snowflake, but discovered, in fact, someone had [already beat me to it](https://github.com/snowflakedb/snowflake-connector-python/issues/235):

![Screenshot of snowflake connector issue on Github](/images/2020-01-04-python-abort-trap-6/github-issue.png)

Someone traced the potential cause to something in the oscrypto library [loading openssl](https://github.com/snowflakedb/snowflake-connector-python/issues/235#issuecomment-554058505), causing it to crash, but that's where my trail ended, since no one could figure out a workaround to that, especially since oscrypto was created as a [split from asn1crypto](https://github.com/wbond/asn1crypto/blob/master/changelog.md#100).

Since by this time I had realized that there was no easy good fix, and that this was actively blocking my work, I decided that I might as well investigate how to fix this once and for all.

### Tracing the source of the bug

The first step to fixing was of course, to identify the offending line of code in the dependency libraries that would trigger it. I use [PyCharm ](https://www.jetbrains.com/pycharm/) as my development IDE at work, and some of its great features include both a way to click through to see original code definitions, and a really powerful graphical [debugger](https://en.wikipedia.org/wiki/Debugger) that is very similar in usability to [IntelliJ](https://www.jetbrains.com/idea/)'s debugger.

However the debugger is not useful if I can't figure out where to set possible breakpoints.

I tried tracing through from `snowflake.connector.connect()` but soon got bogged down since it runs through the requests library as well. I took a step away for lunch/coffee and when I came back, I realized based on the hint above that it was probably a problem in `oscrypto`, I looked for where `oscrypto` was referenced in the snowflake library and added two breakpoints; one where it was imported, and one where the function was being used.

[![First breakpoint](/images/2020-01-04-python-abort-trap-6/breakpoint-1.png)](https://github.com/snowflakedb/snowflake-connector-python/blob/f2f3f998f04666ed5e3bf2e0067856c459cc47c4/ocsp_asn1crypto.py#L20)

[![Second breakpoint](/images/2020-01-04-python-abort-trap-6/breakpoint-2.png)](https://github.com/snowflakedb/snowflake-connector-python/blob/f2f3f998f04666ed5e3bf2e0067856c459cc47c4/ocsp_asn1crypto.py#L317)

Cool, let's try running the debugger and see if dig further.

[![GIF/Video of crash after clicking through one breakpoint](/images/2020-01-04-python-abort-trap-6/early-crash.gif)](/images/2020-01-04-python-abort-trap-6/early-crash.mov)

Woah that's weird! It crashed on the import?

### Digging into the import

After verifying that it was that one import line (by adding a third breakpoint and seeing if the next import succeeded at all), I decided to dig in further to see how that import was causing problems.

[![GIF/Video of setting breakpoints](/images/2020-01-04-python-abort-trap-6/add-breakpoints.gif)](/images/2020-01-04-python-abort-trap-6/add-breakpoints.mov)

I then stumbled upon how the libcrypto libraries were being determined on Mac, and how they were being loaded, in a file called [`_libcrypto_cffi.py`](https://github.com/wbond/oscrypto/blob/a9f577499244e8c8645065be0f495de77447f0cd/oscrypto/_openssl/_libcrypto_cffi.py#L25):

```
libcrypto_path = _backend_config().get('libcrypto_path')
if libcrypto_path is None:
    libcrypto_path = find_library('crypto')
if not libcrypto_path:
    raise LibraryNotFoundError('The library libcrypto could not be found')

try:
    vffi = FFI()
    vffi.cdef("const char *SSLeay_version(int type);")
    version_string = vffi.string(vffi.dlopen(libcrypto_path).SSLeay_version(0)).decode('utf-8')
except (AttributeError):
    vffi = FFI()
    vffi.cdef("const char *OpenSSL_version(int type);")
    version_string = vffi.string(vffi.dlopen(libcrypto_path).OpenSSL_version(0)).decode('utf-8')

is_libressl = 'LibreSSL' in version_string
```

Aha! This might be where we're running into problems! Let's try the debugger and see if we can isolate the crash:

[![GIF/Video of isolating the crash](/images/2020-01-04-python-abort-trap-6/late-crash.gif)](/images/2020-01-04-python-abort-trap-6/late-crash.mov)

Bingo! We isolated the location of the problem!

### Filing and fixing the bug

![Screenshot of oscrypto issue on Github](/images/2020-01-04-python-abort-trap-6/github-issue-2.png)

Now that I found where the issue was, I decided to [file an issue](https://github.com/wbond/oscrypto/issues/35) with the [oscrypto](https://github.com/wbond/oscrypto) project on github. But filing the bug probably was not going to mean it was going to get fixed overnight, so I decided I better investigate to see if I can find make a fix.

Since this seemed to be a Catalina-specific issue, and Apple recommended that we pin to specific versions of the libcrypto dylibs, it seemed like the obvious fix would be to do just that. So I proposed the [following change](https://github.com/wbond/oscrypto/pull/36/files):

```
# if we are on catalina, we want to strongly version libcrypto since unversioned libcrypto has a non-stable ABI
if sys.platform == 'darwin' and platform.mac_ver()[0].startswith('10.15') and \
        libcrypto_path.endswith('libcrypto.dylib'):
    # libcrypto.42.dylib is in libressl-2.6 which as a OpenSSL 1.0.1-compatible API
    libcrypto_path = libcrypto_path.replace('libcrypto.dylib', 'libcrypto.42.dylib')
```

and

```
# if we are on catalina, we want to strongly version libssl since unversioned libcrypto has a non-stable ABI
if sys.platform == 'darwin' and platform.mac_ver()[0].startswith('10.15') and libssl_path.endswith('libssl.dylib'):
    # libssl.44.dylib is in libressl-2.6 which as a OpenSSL 1.0.1-compatible API
    libssl_path = libssl_path.replace('libssl.dylib', 'libssl.44.dylib')
```

Why `42` for crypto vs `44` for ssl? As per [this discussion](https://github.com/wbond/oscrypto/issues/35#issuecomment-556870568):

```
$ for i in `ls libcrypto.*.dylib`; do echo -n $i:; strings $i | grep libressl | head -n1 | cut -d'/' -f9; echo; done
...
libcrypto.42.dylib:libressl-2.6
...
```

```
$ for i in `ls libssl.*.dylib`; do echo -n $i:; strings $i | grep libressl | head -n1 | cut -d'/' -f9; echo; done
...
libssl.44.dylib:libressl-2.6
...
```

We didn't have a problem with `libssl` yet, but better safe than sorry!

### Testing the fix

This was a little bit tricky, due to the fact that this was in a library pulled as a dependency. So to test this, I cloned a copy of the repo down, added my fix, and installed from that local copy by installing it as an egg:

```
pip install file:///Users/shh/Development/github/oscrypto#egg=oscrypto
```

Running the debugger again in fact confirms that the fix works!

### Merge and release

As of this writing, I am happy to say that the fix was merged shortly after I [made the PR](https://github.com/wbond/oscrypto/pull/36), and [released in v1.1.1](https://github.com/wbond/oscrypto/issues/35#issuecomment-570789336) of oscrypto! Future users should no longer be running into this problem :).

Thank you for reading!

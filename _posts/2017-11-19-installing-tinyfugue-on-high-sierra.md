---
layout: post
title: Installing TinyFugue on Mac with Python Bindings
---

<p class="message">
 For 99% of players who don't require Python bindings, you can save yourself a lot of headache by installing <code>tf</code> via brew: 
 <br /><code>brew install tiny-fugue</code>
 <br />
 For the rest of us, please sit tight. ;)
 </p>
 
---

After upgrading to Mac OS X High Sierra, my compiled TinyFugue stopped working. Referring once again to [Ron Dippold](http://sizer99.com/tf/)'s excellent resource on TinyFugue scripting, I found the link to the pre-compiled binaries was broken.

While trying to run `make`, the build failed, with errors about incompatible architectures:
```
Undefined symbols for architecture x86_64:
  "_pcre_info", referenced from:
      _complete_macro in macro.o
      _tf_reg_compile_fl in pattern.o
ld: symbol(s) not found for architecture x86_64
clang: error: linker command failed with exit code 1 (use -v to see invocation)
make[1]: *** [tf] Error 1
make: *** [files] Error 2
```

Unfortunately, tinyfugue was calling a legacy API method `pcre_info` which has since been removed.

Long story short, here are the steps I took to get TinyFugue installed on the latest Mac OS (10.13.1 High Sierra at the time of this writing):
 
 <p class="message">
 Before you begin, it's a good idea to ensure the necessary development tools are installed. You can confirm this by running:
 <br /><code>sudo xcode-select --install</code>
 </p>
 
 * Download the [latest ZIP source for TinyFugue on Sourceforge](https://sourceforge.net/projects/tinyfugue/files/tinyfugue/5.0%20beta%208/tf-50b8.zip/download) (we're using TinyFugue version 5.0 beta 8 for this example)
 * Extract the TinyFugue files (or double-click on the archive) : 
 ```
 ➜  unzip -a tf-50b8.zip
 ➜  rm tf-50b8/help/html2tf
 ```
 As per Ron's instructions, we needed to remove the pre-built `html2tf` library.
 * Download Ron's original [patch file](http://sizer99.com/tf/tf-python-patch) and save to the `tf-50b8` directory
 * Apply Ron's patches:
  ```
  ➜  cd tf-50b8
  # try a dry-run first to see if the patch succeeds
  ➜  patch -p 1 -u -N --dry-run < tf-python-patch 
  ➜  patch -p 1 -u -N < tf-python-patch
  ```
 * Download the [second patch file]({{site.baseurl}}/public/assets/tf-pcre-patch) and save to the `tf-50b8` directory. Apply the second set of patches:
  ```
  # try a dry-run first to see if the patch succeeds
  ➜  patch -p 1 -u -N --dry-run < tf-pcre-patch 
  ➜  patch -p 1 -u -N < tf-pcre-patch
  ```
 * With the patches applied, you can now proceed to configuring and building the binary. 
  ```
  ➜  ./configure
  ➜  make
  ➜  make install
  ```
* Once the install completes, the `tf` binary should now be available on your path.
  
If there's a request for it, I can look into creating a homebrew package with the patches already applied. 
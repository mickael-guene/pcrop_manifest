# ARM PCROP toolset
ARM PCROP toolset implements needed linker and compiler modifications to support
STM32 'Proprietary Code Read-Out Protection' aka [PCROP](http://www.st.com/content/ccc/resource/technical/document/application_note/b4/14/62/81/18/57/48/05/DM00075930.pdf/files/DM00075930.pdf/jcr:content/translations/en.DM00075930.pdf).

PCROP forbids data read access to some code areas (only fetch access is allowed).
This protection prevents usage of literal pools (since one cannot load
data from code sections), so compilers have to use specific code
sequence to generate constants. This is particularly a problem to
generate symbol addresses.

## How to build
Following installation instructions have been tested on an Ubuntu 14.04 docker
image (docker pull ubuntu:14.04).

* install prerequisites

First install following packages
```
~/pcrop $ apt-get install build-essential texinfo flex bison libncurses5-dev curl git python wget automake autoconf libtool pkg-config zlib1g-dev libglib2.0-dev
```
Then install repo if you don't have it in your path
```
~/pcrop $ mkdir ~/bin
~/pcrop $ PATH=~/bin:$PATH
~/pcrop $ curl https://storage.googleapis.com/git-repo-downloads/repo > ~/bin/repo
~/pcrop $ chmod a+x ~/bin/repo
```
* fetch the sources
```
~/pcrop $ repo init -u https://github.com/mickael-guene/pcrop_manifest -b master
~/pcrop $ repo sync
```
* build toolchain package
```
~/pcrop $ ./scratch/build/scripts/build.sh
```

* build runtime package
```
~/pcrop $ ./scratch/build/scripts/build_runtime.sh
```

At the end of the process you will find two tarballs in the newly created 'out'
directory. One that contains the toolset and the other one that contains
umeq and umeq linker script.

To speed up your build you can set JOBNB environment variable to the value you
want to use $JOBNB parallel jobs.

You can test your toolset is functional by running sanity check.

* sanity check
```
~/pcrop $ ./scratch/build/scripts/sanity.sh
...
Hello from arm pcrop
~/pcrop $
```

## How to install it
* install toolset

For that just untar in a directory the tarball you have generated during compilation.
```
~/pcrop $ mkdir install_test
~/pcrop $ tar -C install_test -xf out/toolset-20160520-110929-82fc8bcc-armv6s-m.tgz
```
* setup path and test your toolset is alive.
```
~/pcrop $ export PATH=~/pcrop/install_test/bin:${PATH}
~/pcrop $ arm-none-eabi-gcc -v

[![Build Status](https://drone.yoedistro.org/api/badges/kraj/meta-clang/status.svg)](https://drone.yoedistro.org/kraj/meta-clang)

# meta-clang (a C language family frontend and LLVM compiler backend)

This layer provides [clang/llvm](http://clang.llvm.org/) as alternative to your system
C/C++ compiler for OpenEmbedded based distributions along with gcc

# Getting Started

```shell
git clone git://github.com/openembedded/openembedded-core.git
cd openembedded-core
git clone git://github.com/openembedded/bitbake.git
git clone git://github.com/kraj/meta-clang.git

$ . ./oe-init-build-env
```

Add meta-clang overlay
```
bitbake-layers add-layer ../meta-clang
```

Check `conf/bblayers.conf` to see that meta-clang is added to layer mix e.g.

# Default Compiler Switch

Note that by default gcc will remain the system compiler, however if you wish
clang to be the default compiler then set

```shell
TOOLCHAIN ?= "clang"
```

in `local.conf`, this would now switch default cross-compiler to be clang 
you can select clang per recipe too by writing bbappends for them containing

```shell
TOOLCHAIN = "clang"
```

# Default C++ Standard Library Switch

Note that by default clang libc++ is default C++ standard library, however if you wish
to keep GNU libstdc++ to be the default then set

```shell
LIBCPLUSPLUS = ""
```

in `local.conf`.
You can select libstdc++ per package too by writing bbappends for them containing

```shell
LIBCPLUSPLUS_toolchain-clang_pn-<recipe> = ""
```

# Default Compiler Runtime ( Compiler-rt + libc++ )

By default, clang build from meta-clang uses clang runtime ( compiler-rt + libc++ + libunwind ) out of box
However, it is possible to switch to using gcc runtime as default, In order to do that
following settings are needed in site configurations e.g. in `local.conf`

```shell
TOOLCHAIN ?= "clang"
LIBCPLUSPLUS = ""
COMPILER_RT = ""
UNWINDLIB = ""

```

# Removing clang from generated SDK toolchain

clang based cross compiler is automatically included into the generated SDK using `bitbake meta-toolchain` or
`bitbake -cpopulate_sdk <image>` in circumstanced where clang is not expected to be part of SDK, then reset `CLANGSDK`
variable in `local.conf`

```shell
CLANGSDK = ""
```

# Building

Below we build for qemuarm machine as an example

```shell
$ MACHINE=qemuarm bitbake core-image-full-cmdline
```
# Running

```shell
$ runqemu nographic
```

# Limitations

Few components do not build with clang, if you have a component to add to that list
simply add it to conf/nonclangable.inc e.g.

```shell
TOOLCHAIN_pn-<recipe> = "gcc"
```

and OE will start using gcc to cross compile that recipe.

And if a component does not build with libc++, you can add it to `conf/nonclangable.inc` e.g.

```shell
CXX_remove_pn-<recipe>_toolchain-clang = " -stdlib=libc++ "
```
# Classic Cross-Compile SDK Use With CMake and Autotools

The classic cross-compilation SDK from Yocto uses the GCC specified in the GCCVERSION that is chosen by Yocto at build time by default.  There are six total environment variables set by Yocto when you stage up the Classic SDK for use:

    CC
    CXX
    CPP
    CLANGCC
    CLANGCXX
    CLANGCPP

The first three contain GCC specific configurations that work correctly for the GCC version in the SDK  These will work correctly to build with the GCC cross-compilation toolchain with CMake and Autotools out of the box.  If you want to make clang available to be used the same way, you only need to copy the contents of the CLANG<foo> env vars over to their respective "GCC" equivalents (e.g. CC=$CLANGCC).  Once done, CMake and Autotools will correctly work with clang instead.


# Dependencies

```shell
URI: git://github.com/openembedded/openembedded-core.git
branch: master
revision: HEAD

URI: git://github.com/openembedded/bitbake.git
branch: master
revision: HEAD
```

# Contributing

You are encouraged to follow Github Pull request workflow
to share changes and following commit message guidelines are recommended [OE patch guidelines](https://www.openembedded.org/wiki/Commit_Patch_Message_Guidelines)

Layer Maintainer: [Khem Raj](<mailto:raj.khem@gmail.com>)

# CMLFS - Clang Musl Linux From Scratch

CMLFS can either mean "Clang-built Musl Linux from Scratch" or "Clang MLFS". It started as a hobby to see if a Linux system can be built with clang as primary toolchain and GCC as secondary (for packages that cannot be built with clang). This is based on [Linux From Scratch](www.linuxfromscratch.org) and my previous work [MLFS](https://github.com/dslm4515/Musl-LFS). Big thanks to [Chimera-Linux](https://chimera-linux.org) that had patches that fixed a lot of issues.

## DISCLAIMER

Use at you own risk. This builds a Unix-like system that may not be stable enough as a 'daily driver' for most users. Security-hardening of the built system is beyound the scope of this project. This repo started as means to backup and archive my work.

## Getting Started

The master branch may be unstable when I am working on upgrading CMLFS. 

To build this project, pick a branch other than master OR choose a tag. Each branch & tag are based on the LLVM version.
As of this writing, llvm-15.0.6 is stable. Older branches maybe incomplete or unstable (may not compile).

When I have time later, I will write a more thorough introduction for users new to CMLFS.

## Specification
<ul>
<li>C Runtime Library (system libc): Musl </li>
<li>Default C Compiler: clang (LLVM)</li>
<li>Default C++ compiler: clang++ (LLVM)</li>
<li>Default linker: lld (LLVM)</li>
<li>Default binary tools: elftoolchain</li>
<li>Secondary C Compiler: GCC</li>
<li>Secondary C++ compiler: GCC</li>
<li>Secondary binary tools: GNU Binutils & LLVM</li>
<li>Secondary linker(s): bfd, gold</li>
<li>C++ standard library: libcxx (LLVM)</li>
<li>C++ ABI library: libcxxabi (LLVM)</li>
<li>Unwinding Library: libunwind (LLVM)</li>
<li>Init system: skarnet's S6 & S6-rc</li>
<li>Device manager: Udev </li>
<li>TLS Implementaion: LibreSSL</li>
<li>Secondary TLS Implementaion: OpenSSL</li>
<li>System Shell: Bash </li>
<li>System Gettext: gettext-tiny</li>
<li>Curses Library: netbsd-curses </li>
</ul>

## Supported Architectures

<ul>
<li>AMD64/x86_64: Toolchains and final system build sucessfully (musl & glibc hosts) .</li>
<li>i686: Pending</li>
<li>AARCH64/ARM64: Pending</li>
<li>ARMV7L: Pending</li>
</ul>

## Goals

<ul>
<li> [x] Build a toolchain (llvmtools) with LLVM+stage1_clang but without GCC</li>
<li> [x] Build final root filesystem with LLVM</li>
<li> [x] Set default linker as lld(LLVM)</li>
<li> [x] Set default C++ standard library as libcxx(LLVM)</li>
<li> [x] Set default C++ ABI library as libcxxabi(LLVM)</li>
<li> [x] Set default stack unwinding library as libunwind(LLVM)</li>
<li> [x] Eliminate dependacy on GCC's libgcc_s</li>
<li> [x] Build GCC as a secondary systen compiler. </li>
<li> [x] Build toolchain (llvmtools) with GCC as secondary compiler</li>
<li> [x] Merge cross-tools build with cgnutools </li>
<li> [x] Build successfully on a Glibc host </li>
<li> [x] Build final system without GCC </li>
<li> [x] Replace binutils with elftoolchain </li>
<li> [ ] Reduce LLVM size & build time for cgnutools and llvmtools </li>
<li> [ ] Build on aarch64</li>
</ul>

## Host System Requirements

<ul>
 <li>CMake</li>
 <li>Ninja/Samurai</li>
 <li>wget/cURL</li>
</ul>

## Required Base Development tools (may refer to LFS)
<ul>
 <li>bash 3.2 (/bin/sh should be a symbolic or hard link to bash) </li>
 <li>binutils 2.25 </li>
 <li>bison 2.7 (/usr/bin/yacc should be a link to bison or small script that executes bison) </li>
 <li>bzip2 1.0.4 </li>
 <li>coreutils 6.9 </li>
 <li>diffutils 2.8.1 </li>
 <li>findutils 4.2.31 </li>
 <li>gawk 4.0.1 (/usr/bin/awk should be a link to gawk) </li>
 <li>GCC 6.2 (including the C++ compiler, g++) </li>
 <li>Glibc 2.11 / Musl Libc 1.20 </li>
 <li>Grep 2.5.1a </li>
 <li>gzip 1.3.12 </li>
 <li>linux kernel 3.2 (not sure if it matters, as most distros are running 4.x/5.x kernels</li>
 <li>m4 1.4.10</li>
 <li>make 4.0 </li>
 <li>patch 2.5.4 </li>
 <li>Python 3.4 </li>
 <li>sed 4.1.5 </li>
 <li>tar 1.22 </li>
 <li>texinfo 4.7 </li>
  <li>xz 5.0.0 </li>
</ul>
 * (if hostdistro is MLFS/LFS, then all development packages are installed)

## Current Method

Build 'cross-tools' with [Mussel](https://github.com/firasuke/mussel) to cross-compile a stage0 LLVM+clang. This stage0 clang will still link to `libgcc_s` [in cgnutools] but will later be used to build a stage1 clang free of `libbgcc_s`. The goal is to build clang+friends with clang and not GCC.

* Some packages can be built once to be used by the toolchain [llvmtools] and the final system, but will be built twice to make it easy to implement a package managment system [which is outside the scope of this project]. 

<ol>
<li>Bootstrap build of cgnutools with mussel</li>
<li>Use mussel-built toolchain (cgnutools) to build stage0 LLVM+clang</li>
<li>Use cgnutools to build stage1 LLVM+clang with sysroot at llvmtools with stage0 LVVM+clang</li>
<li>Build enough of llvmtools to enter a chroot with stage1 LLVM+clang</li>
<li>Build the rest of llvmtools under chroot </li>
<li>Build final system with llvmtools </li>
</ol>

## Issues
<ul>
<li>Test for C++11/14 fails when testing stage0 & stage1 LLVM's. Not sure what issue this will cause or if test needs to be revised..</li>
<li>Coreutils will not build for llvmtools under 32-bit x86 (i386/i586/i686)</li>
<li>Ninja for llvmtools fails to compile with python3. For now use cmake instead of python3</li> 
</ul>

## Change log

<ul>
<li>4.0.0: Upgraded to LLVM 17.0.5 </li>
<li>3.0.0: Upgraded to LLVM-15.0.6. cgnutools is now bootstrapped with mussel. Replaced binutils with elftoolchain. Most of llvmtools will be build under chroot to avoid contamination from host. </li>
<li>2.0.0: Upgraded to LLVM-12.0.0. Upgraded GCC to 10.3.1-x Replace ninja with samurai. Replace zlib with zlib-ng. Patched elfutils to build libelf under clang. No longer using /llvmtools/gnu and /opt/gnu.</li>
<li>1.2.0: Incomplete: LLVM-11.0.0, Install GCC & Binutils in /llvmtools & /usr instead of /llvmtools/gnu and /opt/gnu </li>
<li>1.1.0: Sucessfully merged cross-tools and cgnutools to include GCC & binutils.</li>
<li>1.0.0: Sucessfully built on x86_64. GCC built as secondary compiler in /opt/gnu </li>
<li>0.1.3: Configure Stage1 clang correctly with x86_64-pc-linux-musl.cfg.</li>
<li>0.1.2: Use stage0 to build a stage1 clang...Stage1 clang will be used in chroot. Stage1 clang fails to compile</li>
<li>0.1.1: Build stage0 clang by building clang, lld, compiler-rt, libunwind, libcxxabi, libcxx together in llvm source tree. Stage0 builds binaries with host's dynamic linker in /lib</li>
<li>0.1.0: Build cross-tools with GCC to build stage 1 clang... first build libunwind, libcxxabi & libcxx - stage1 Clang broken</li>
<li>0.0.0: First attempt, modeled afer Genshen's repo: Stage 2 clang fails to build.</li>
</ul>

## Projects of Interest
<ul>
<li> [Mussel](https://github.com/firasuke/mussel)</li>
<li> [Musl Linux From Scratch](https://github.com/dslm4515/Musl-LFS) - Based on LFS, but uses Musl instead of Glibc </li>
<li> [Beyond MLFS](https://github.com/dslm4515/BMLFS) - The Musl version of LFS's BLFS </li>
<li> [MLFS-S6-Bootscripts](https://github.com/dslm4515/MLFS-S6-Bootscripts) - Boot scripts for CMLFS/MLFS/LFS using skarnet's S6+S6-rc init system </li>
<li> [MLFS-Pkgtool](https://github.com/dslm4515/MLFS-pkgtool) - Musl LFS with Slackware's pkgtools </li>
<li> [Genshen's docker-clang-toolchain](https://github.com/genshen/docker-clang-toolchain)</li>
<li> [Build a freestanding libc++](https://blogs.gentoo.org/gsoc2016-native-clang/2016/05/05/build-a-freestanding-libcxx/) </li>
</ul>

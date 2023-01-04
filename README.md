# CMLFS - Clang Musl Linux From Scratch

CMLFS can either mean "Clang-built Musl Linux from Scratch" or "Clang MLFS". It started as a hobby to see if a Linux system can be built with clang as primary toolchain and GCC as secondary (for packages that cannot be built with clang). This is based on Linux From Scratch (www.linuxfromscratch.org) and my previous work MLFS (https://github.com/dslm4515/Musl-LFS).

# Project Status
Testing new build method: Using mussel toolchain to bootstrap cgnutools.

## Specification
<ul>
<li>C Runtime Library (system libc): Musl </li>
<li>Default C Compiler: clang (LLVM)</li>
<li>Default C++ compiler: clang++ (LLVM)</li>
<li>Default linker: lld (LLVM)</li>
<li>Default binary tools: (LLVM)</li>
<li>Secondary C Compiler: GCC</li>
<li>Secondary C++ compiler: GCC</li>
<li>Secondary binary tools: GNU Binutils</li>
<li>Secondary linker(s): bfd, gold</li>
<li>C++ standard library: libcxx (LLVM)</li>
<li>C++ ABI library: libcxxabi (LLVM)</li>
<li>Unwinding Library: libunwind (LLVM)</li>
<li>Init system: skarnet's S6 & S6-rc</li>
<li>Device manager: Udev </li>
<li>TLS Implementaion: LibreSSL</li>
<li>System Shell: Bash </li>
<li>System Gettext: gettext-tiny</li>
</ul>

## Supported Architectures

<ul>
<li>AMD64/x86_64: In-progress</li>
<li>i686: Pending</li>
<li>AARCH64/ARM64: Pending</li>
<li>ARMV7L: Pending</li>
</ul>

## Goals

<ul>
<li> [x] Build a toolchain (llvmtools) with LLVM+stage1_clang but without GCC</li>
<li> [ ] Build final root filesystem with LLVM</li>
<li> [ ] Set default linker as lld(LLVM)</li>
<li> [ ] Set default C++ standard library as libcxx(LLVM)</li>
<li> [ ] Set default C++ ABI library as libcxxabi(LLVM)</li>
<li> [ ] Set default stack unwinding library as libunwind(LLVM)</li>
<li> [x] Eliminate dependacy on GCC's libgcc_s</li>
<li> [ ] Build GCC as a secondary systen compiler. </li>
<li> [ ] Build toolchain (llvmtools) with GCC as secondary compiler</li>
<li> [x] Build cgnutools with mussel </li>
<li> [x] Build successfully on a Glibc host </li>
<li> [x] Reduce LLVM size & build time for cgnutools and llvmtools </li>
<li> [ ] Build as much of llvmtools under chroot </li>
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
 <li>GCC 6.2 *including the C++ compiler, g++ </li>
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
 * (if host distro is MLFS/LFS, then all development packages are installed)

## Current Method

Build a gcc toolchain with [mussel](https://github.com/firasuke/mussel) to cross-compile stage0 clang. This stage0 clang will still link to `libgcc_s` but will later be used to build a stage1 clang free of `libbgcc_s`. The goal is to build clang+friends with clang and not GCC.
<ol>
<li>Build `cgnutools` with [mussel](https://github.com/firasuke/mussel)</li>
<li>Build a stage0 clang with modified mussel toolchain in `cgnutools`: build clang with patched llvm-project source.</li>
<li>Build a new stage1 clang with stage0 clang. This new stage1 clang will not have GCC libraries. This will install in `llvmtools`.</li>
<li>Using stage0 clang, build up toolchain (llvmtools) to chroot.</li>
<li>Build the rest of llvmtools with stage1 clang. </li>
<li>Build stage2 clang with stage1 clang. This will also be the primary compiler for the final root filesystem.</li>
<li>Build final root filesystem in chroot with stage2 clang and toolchain (llvmtools)</li>
</ol>

## Issues
GCC will not compile with stage1 clang under chroot, but can be cross-compiled with cross-gcc in cgnutools (during build, xgcc fails configure testing for libatomic).

## Change log

<ul>
<li>3.0.0: Upgraded to LLVM-15.0.5 </li>
<li>2.0.0: Upgraded to LLVM-12.0.0. Upgraded GCC to 10.3.1-x Replace ninja with samurai. Replace zlib with zlib-ng. Patched elfutils to build libelf under clang. No longer using /llvmtools/gnu and /opt/gnu.</li>
<li>1.2.0: Incomplete: LLVM=11.0.0, Install GCC & Binutils in /llvmtools & /usr instead of /llvmtools/gnu and /opt/gnu </li>
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
<li> [Musl Linux From Scratch](https://github.com/dslm4515/Musl-LFS) - Based on LFS, but uses Musl instead of Glibc </li>
<li> [Beyond MLFS](https://github.com/dslm4515/BMLFS) - The Musl version of LFS's BLFS </li>
<li> [MLFS-S6-Bootscripts](https://github.com/dslm4515/MLFS-S6-Bootscripts) - Boot scripts for CMLFS/MLFS/LFS using skarnet's S6+S6-rc init system </li>
<li> [MLFS-Pkgtool](https://github.com/dslm4515/MLFS-pkgtool) - Musl LFS with Slackware's pkgtools </li>
<li> [Genshen's docker-clang-toolchain](https://github.com/genshen/docker-clang-toolchain)</li>
<li> [Build a freestanding libc++](https://blogs.gentoo.org/gsoc2016-native-clang/2016/05/05/build-a-freestanding-libcxx/) </li>
</ul>

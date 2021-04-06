# CMLFS - Clang Musl Linux From Scratch

This is based on Linux From Scratch (www.linuxfromscratch.org) but with the goal of building a system with clang & friends from LLVM and Musl Libc.

## Specification
<ul>
<li>C Runtime Library (system libc): Musl </li>
<li>Default C Compiler: clang (LLVM)</li>
<li>Default C++ compiler: clang++ (LLVM)</li>
<li>Default linker: lld (LLVM)</li>
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

AMD64/x86_64
_Other arches will be supported after first successive build._

## Goals

<ul>
<li> [ ] Build a toolchain with LLVM but without GCC</li>
<li> [ ] Build final root filesystem with LLVM</li>
<li> [ ] Set default linker as lld(LLVM)</li>
<li> [ ] Set default C++ standard library as libcxx(LLVM)</li>
<li> [ ] Set default C++ ABI library as libcxxabi(LLVM)</li>
<li> [ ] Set default stack unwinding library as libunwind(LLVM)</li>
<li> [ ] Eliminate dependacy on GCC's libgcc_s</li>
</ul>

## Current Method
Based on Genshen's repo(github.com/genshen/docker-clang-toolchain)
Uses the large source archive that includes all LLVM source like clang, 
compiler-rt, lld, libunwind, libcxxabi, and libcxx.
<ol>
<li>Stage 1: Build clang with GCC libraries</li>
<li>Stage 2: Build new clang with old clang. This clang will not have GCC libraries</li>
<li>Stage 3: Build final root filesystem in chroot</li>
</ol>

## Issues
<ul>
<li>Stage 2 Clang fails to build</li>
<li>Binaries built are still linked to host's libc</li>
<li>LLVM's lld is not used, but wanted.</li>
</ul>

## Change log

<ul>
<li>DEV-01(Pending): Build cross-tools with GCC to build stage 1 clang</li>
<li>DEV-00: First attempt, modeled afer Genshen's repo: Stage 2 clang fails to build.</li>
</ul>

## Projects of Interest
<ul>
<li>Genshen's docker-clang-toolchain - https://github.com/genshen/docker-clang-toolchain</li>
<li>Build a freestanding libc++ - https://blogs.gentoo.org/gsoc2016-native-clang/2016/05/05/build-a-freestanding-libcxx/ </li>
</ul>

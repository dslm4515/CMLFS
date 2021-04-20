# CMLFS - Clang Musl Linux From Scratch

CMLFS can either mean "Clang-built Musl Linux from Scratch" or "Clang MLFS". It started as a hobby to see if a Linux system can be built with clang as primary toolchain and GCC as secondary. This is based on Linux From Scratch (www.linuxfromscratch.org) and my previous work MLFS (https://github.com/dslm4515/Musl-LFS)

## Specification
<ul>
<li>C Runtime Library (system libc): Musl </li>
<li>Default C Compiler: clang (LLVM)</li>
<li>Default C++ compiler: clang++ (LLVM)</li>
<li>Default linker: lld (LLVM)</li>
<li>Default binary tools: (LLVM)</li>
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
<li> [x] Build a toolchain (llvmtools) with LLVM but without GCC</li>
<li> [x] Build final root filesystem with LLVM</li>
<li> [x] Set default linker as lld(LLVM)</li>
<li> [x] Set default C++ standard library as libcxx(LLVM)</li>
<li> [x] Set default C++ ABI library as libcxxabi(LLVM)</li>
<li> [x] Set default stack unwinding library as libunwind(LLVM)</li>
<li> [x] Eliminate dependacy on GCC's libgcc_s</li>
<li> [ ] Build GCC as a secondary compiler system. </li>
</ul>

## Current Method

Build or use 'cross-tools' from [Musl-LFS](https://github.com/dslm4515/Musl-LFS) to cross-compile stage0 clang. This stage0 clang will still link to `libgcc_s` but will later be used to build a stage1 clang free of `libbgcc_s`. The goal is to build clang+friends with clang and not GCC.
<ol>
<li>Build `cross-tools` with GCC</li>
<li>Build a stage0 clang with GCC libraries with `cross-tools`: build clang via llvm source with clang+lld unpacked in `llvm/tools` and libunwind, libcxxabi & libcxx in `lvm/projects`.</li>
<li>Build individually in LLVM source tree libunwind, libcxxabi and libcxx with stage0 clang. </li>
<li>Build a new stage1 clang with stage0 clang. This new stage1 clang will not have GCC libraries</li>
<li>Using stage1 clang, build toolchain for use in chroot</li>
<li>Build final root filesystem in chroot with stage1 clang and toolchain</li>
</ol>

## Issues
<ul>
<li>Clang requires `execinfo.h` - Added libexecinfo to build</li>
<li>libcap still expects gcc - Temp-fix: `ln -sv clang /usr/bin/gcc`</li>
<li>libelf(elfutils) requires a compiler with GNU99 support. Clang doe not have true GNU99 support. Compiles fine with GCC from cross-tools</li> 
</ul>

## Change log

<ul>
<li>0.1.3: configure Stage1 clang correctly with x86_64-pc-linux-musl.cfg.
<li>0.1.2: Use stage0 to build a stage1 clang...Stage1 clang will be used in chroot. Stage1 clang fails to compile</li>
<li>0.1.1: Build stage0 clang by building clang, lld, compiler-rt, libunwind, libcxxabi, libcxx together in llvm source tree. Stage0 builds binaries with host's dynamic linker in /lib</li>
<li>0.1.0: Build cross-tools with GCC to build stage 1 clang... first build libunwind, libcxxabi & libcxx - stage1 Clang broken</li>
<li>0.0.0: First attempt, modeled afer Genshen's repo: Stage 2 clang fails to build.</li>
</ul>

## Projects of Interest
<ul>
<li>Genshen's docker-clang-toolchain - https://github.com/genshen/docker-clang-toolchain</li>
<li>Build a freestanding libc++ - https://blogs.gentoo.org/gsoc2016-native-clang/2016/05/05/build-a-freestanding-libcxx/ </li>
</ul>

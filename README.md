# CMLFS - Clang Musl Linux From Scratch

CMLFS can either mean "Clang-built Musl Linux from Scratch" or "Clang MLFS". It started as a hobby to see if a Linux system can be built with clang as primary toolchain and GCC as secondary (for packages that cannot be built with clang). This is based on [Linux From Scratch](www.linuxfromscratch.org) and my previous work [MLFS](https://github.com/dslm4515/Musl-LFS).

## Specification
<ul>
<li>C Runtime Library (system libc): Musl </li>
<li>Default C Compiler: clang (LLVM)</li>
<li>Default C++ compiler: clang++ (LLVM)</li>
<li>Default linker: lld (LLVM)</li>
<li>Default binary tools: elftoolchain</li>
<li>Secondary C Compiler: GCC</li>
<li>Secondary C++ compiler: GCC</li>
<li>Secondary binary tools: GNU Binutils</li>
<li>Secondary linker(s): bfd, gold</li>
<li>C++ standard library: libcxx (LLVM)</li>
<li>C++ ABI library: libcxxabi (LLVM)</li>
<li>Unwinding Library: libunwind (LLVM)</li>
<li>Init system: skarnet's S6 & S6-rc</li>
<li>Device manager: Udev </li>
<li>TLS Implementaion: LibreSSL+OpenSSL </li>
<li>System Shell: Bash + Dash </li>
<li>System Gettext: gettext-tiny</li>
<li>Userland: bsdutils </li>
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
<li> [ ] Build successfully on a Glibc host </li>
<li> [ ] Reduce LLVM size & build time for cgnutools and llvmtools </li>
<li> [ ] Build as much of llvmtools under chroot </li>
<li> [ ] Build on aarch64</li>
<li> [ ] Replace coreutils with bsdutils</li>
<li> [ ] Replace binutils with elftoolchain>/li>
</ul>

## Host System Requirements

<ul>
 <li>CMake</li>
 <li>Ninja/Samurai</li>
 <li>wget/cURL</li>
 <li>meson (if building chimerautils for chroot)</li>
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
 <li>GCC 6.2 , including the C++ compiler, g++ </li>
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

Cross compile enough packages to create a chroot enviroment to build the final system.

<ol>
<li>Build mussel toolchain for a cross-gcc</li>
<li>Cross-compile [with cross-gcc] musl-libc, kernel-headers, zlib-ng, libatomic=chimera, fortify-headers for llvmtools. Will also be used for building stage0 clang.</li>
<li>Build libunwind for cross-gcc. Building stage0 clang expects libunwind alread present.</li>
<li>Use cross-gcc to build stage0 clang, but install to cgnutools and set sysroot at llvmtools.</li>
<li>Use stage0 clang to build stage1 clang. Stage1 clang will be gcc-free.</li>
<li>Build enough of llvmtools with stage0 clang for chroot</li>
<li>Enter chroot with llvmtools and build the final system.</li>
</ol>

# CMLFS - Clang Musl Linux From Scratch

This is based on Linux From Scratch (www.linuxfromscratch.org) but with the goal of building a system with clang & friends from LLVM and Musl Libc.

## LLVM Version: 11.0.0

## Supported Architectures

AMD64/x86_64

Other arches will be supported after first successive build.

## Goals

<ul>
<li> [ ] Build a toolchain with LLVM but without GCC</li>
<li> [ ] Build final root filesystem with LLVM</li>
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

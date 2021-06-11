---
title: "llvm安装"
date: 2021-04-02T15:24:27+08:00
description: "某次信息安全综合实践教学须使用llvm进行编译一些模糊测试工具"
tags: 
   - 工具使用
categories:
   - Development
   - Record
draft: false
---

### 安装相应依赖

`sudo apt-get install -y libc6-dev binutils libgcc-{version}-dev build-essential make cmake git python2.7 python3-distutils`

注：libgcc-{version}-dev中的version就是gcc的版本，如果是9.x.x就是9.

### 安装

1. 下载整个压缩包，我这里使用的是 [github](https://github.com/llvm/llvm-project/releases/download/llvmorg-11.1.0/clang+llvm-11.1.0-x86_64-linux-gnu-ubuntu-20.10.tar.xz) 

2. 建立src和work文件夹，命令是

   ```
   mkdir -p $WORK_DIR/src
   mkdir -p $WORK_DIR/work/llvm
   ```

3. 将刚才的压缩包解压到src文件夹下

4. 进入到work/llvm文件夹下，进行cmake操作

   ```
   # 命令规范：cmake -G <generator> [options] ../llvm
   cmake -G "Unix Makefiles" -DCMAKE_BUILD_TYPE=Release -DLIBCXX_ENABLE_SHARED=OFF -DLIBCXX_ENABLE_STATIC_ABI_LIBRARY=ON -DLLVM_TARGETS_TO_BUILD="X86" {../llvm文件路径} -DLLVM_ENABLE_PROJECTS="clang;libcxx;libcxxabi;compiler-rt;clang-tools-extra;openmp;lldb;lld"
   # 注：{../llvm文件路径}这是llvm所在的文件路径，如果按照顺序走下来，这个地方应该是../../src/llvm-x.x.x/llvm
   ```

5. 利用刚才的命令，在work/llvm文件夹下已经制作好了makefile，此时执行以下命令：

   ```
   make -j$(nproc)
   注：-j命令是多核编译，有助于加快速度
   ```


### 其它参考资料 

   Some common build system generators are:

- `Ninja` — for generating [Ninja](https://ninja-build.org) build files. Most llvm developers use Ninja.

- `Unix Makefiles` — for generating make-compatible parallel makefiles.

- `Visual Studio` — for generating Visual Studio projects and solutions.

- `Xcode` — for generating Xcode projects.

- 这里使用Unix Makefiles

- `-DLLVM_ENABLE_PROJECTS='...'` — semicolon-separated list of the LLVM subprojects you’d like to additionally build. Can include any of: clang, clang-tools-extra, libcxx, libcxxabi, libunwind, lldb, compiler-rt, lld, polly, or debuginfo-tests.

  For example, to build LLVM, Clang, libcxx, and libcxxabi, use `-DLLVM_ENABLE_PROJECTS="clang;libcxx;libcxxabi"

- `-DCMAKE_INSTALL_PREFIX=directory` — Specify for *directory* the full pathname of where you want the LLVM tools and libraries to be installed (default `/usr/local`).

- `-DCMAKE_BUILD_TYPE=type` — Valid options for *type* are Debug, Release, RelWithDebInfo, and MinSizeRel. Default is Debug.

- `-DLLVM_ENABLE_ASSERTIONS=On` — Compile with assertion checks enabled (default is Yes for Debug builds, No for all other build types).

- 例子：

   cmake -G "Unix Makefiles" -DCMAKE_BUILD_TYPE=Release --enable-optimized --enable-targets=host-only  {../llvm文件路径} -DLLVM_ENABLE_PROJECTS="clang;libcxx;libcxxabi;compiler-rt;clang-tools-extra;openmp;lldb;lld" 
   
   -DLIBCXX_ENABLE_SHARED=OFF -DLIBCXX_ENABLE_STATIC_ABI_LIBRARY=ON  -DCMAKE_BUILD_TYPE=Release -DLLVM_TARGETS_TO_BUILD="X86"

### 报错：

- ModuleNotFoundError: No module named 'distutils.spawn'：
  - sudo apt-get install python3-distutils
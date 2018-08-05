# Toolchain
工具链

Fuchsia is using Clang as the official compiler.
Fuchsia使用Clang作为官方编译器。


## Building Clang
建立Clang


The Clang CMake build system supports bootstrap (aka multi-stage) builds. We use two-stage bootstrap build for the Fuchsia Clang compiler.
Clang CMake构建系统支持引导(又名多阶段)构建。我们为Fuchsia Clang编译器用上两级引导构建。


The first stage compiler is a host-only compiler with some options set needed for the second stage. The second stage compiler is the fully 
optimized compiler intended to ship to users.
第一阶段编译器是一个只包含主机的编译器，其中包含第二阶段所需的一些选项集。第二阶段编译器是旨在提供给用户的完全优化的编译器。


Setting up these compilers requires a lot of options. To simplify the configuration the Fuchsia Clang build settings are contained in CMake cache
files which are part of the Clang codebase.
设置这些编译器需要很多选项。为了简化配置，Fuchsia Clang构建设置包含在CMake缓存文件中，CMake缓存文件是Clang代码库的一部分。


The example commands below use `${LLVM_SRCDIR}` to refer to the root of your LLVM source tree checkout, and assume that the additional repositories 
are checked out into their canonical subdirectories of the main LLVM checkout can cut & paste these commands into a shell after setting the `
LLVM_SRCDIR` variable, e.g.:
下面的示例命令使用`${LLVM_SRCDIR}`来引用LLVM源树签出的根，并假定其他存储库已签出到主LLVM签出的规范子目录中(“工具/Clang”中的Clang 、“运行时/编译器- rt”中的“编译器- rt”等)。
设置`LLVM_SRCDIR`变量后，可以将这些命令剪切并粘贴到外壳中，例如:


```bash
LLVM_SRCDIR=${HOME}/llvm
```


Before building the runtime libraries that are built along with the compiler, you need a Zircon `sysroot` built.  This comes from the Zircon build 
and sits in the `sysroot` subdirectory of the main Zircon build directory.  For example, `.../build-x86/sysroot`. In the following commands, the 
string `${FUCHSIA_${arch}_SYSROOT}` stands in for this absolute directory name.  You can cut & paste these commands into a shell after setting the `
FUCHSIA_${arch}_SYSROOT` variable, e.g.:
在随编译器一起构建的运行时库之前，需要构建一个Zircon 'sysroot'。这来自Zircon 构建，位于主Zircon 构造目录的子目录中。例如，`.../build-x86/sysroot`。在以下命令中，字符串“% FUCHSIA _ $ { arch } _ SYSROOT }”表示此绝对目录名。设置`FUCHSIA_${arch}_SYSROOT`变量后，可以将这些命令剪切并粘贴到外壳中，例如:


```bash
make x86
FUCHSIA_x86_64_SYSROOT=`pwd`/build-x86/sysroot


make arm64
FUCHSIA_aarch64_SYSROOT=`pwd`/build-arm64/sysroot
```


You can build a Fuchsia Clang compiler using the following commands.
These must be run in a separate build directory, which you must create.
This directory can be a subdirectory of `${LLVM_SRCDIR}` so that you
use `LLVM_SRCDIR=..` or it can be elsewhere, with `LLVM_SRCDIR` set
to an absolute or relative directory path from the build directory.
您可以使用以下命令构建一个Fuchsia Clang编译器。
这些必须在您必须创建的单独的生成目录中运行。
此目录可以是＄{ LLVM _ SRCDIR }的子目录，以便您使用` ` llvm _ SRCDIR =..也可以在其他位置，将`LLVM_SRCDIR`设置为从生成目录开始的绝对或相对目录路径。


```bash
cmake -G Ninja -DFUCHSIA_x86_64_SYSROOT=${FUCHSIA_x86_64_SYSROOT} -DFUCHSIA_aarch64_SYSROOT=${FUCHSIA_aarch64_SYSROOT} -C ${LLVM_SRCDIR}/tools/clang/cmake/caches/Fuchsia.cmake ${LLVM_SRCDIR}
ninja stage2-distribution
```


To install the compiler just built into `/usr/local`, you can use the
following command:
若要安装刚刚内置到` / usr / local '中的编译器，可以使用
以下命令:


```bash
ninja stage2-install-distribution
```


To use the compiler just built without installing it into a system-wide
shared location, you can just refer to its build directory explicitly as
`${LLVM_OBJDIR}/tools/clang/stage2-bins/bin/` (where `LLVM_OBJDIR` is
your LLVM build directory).  For example, in a Zircon build, you can
pass the argument:
要使用刚生成的编译器而不将其安装到系统范围的共享位置，您可以将其生成目录显式引用为“＄{ LLVM _ OBJDIR } / tools / clang / stage 2 - bins / bin /”(其中，` LLVM _ OBJDIR '是您的LLVM生成目录)。
例如，在Zircon 建立中，您可以传递引数:


```bash
CLANG_TOOLCHAIN_PREFIX=${LLVM_OBJDIR}/tools/clang/stage2-bins/bin/
```


(Note: that trailing slash is important.)
(注意:尾随斜杠很重要。) 


You need CMake version 3.8.0 and newer to execute these commands.
This was the first version to support Fuchsia.
执行这些命令需要CMake 3 . 8 . 0和更高版本。
这是第一个支持Fuchsia的版本。


Note that the second stage build uses LTO (Link Time Optimization) to achieve
better runtime performance of the final compiler. LTO often requires a large
amount of memory and is very slow. Therefore it may not be very practical for
day-to-day development.
请注意，第二阶段构建使用LTO (链路时间优化)来实现最终编译器更好的运行时性能。
LTO通常需要大量内存，而且速度非常慢。
因此，它对于日常开发可能不是很实用。


### Using Monorepo Layout
使用单报告布局


Alternatively, it is also possible to use the new [monorepo layout](https://llvm.org/docs/Proposals/GitHubMove.html#monorepo-variant).
When using this layout, each sub-project has its own top-level directory.
或者，也可以使用新的[monorepo layout](https://llvm.org/docs/Proposals/GitHubMove.html#monorepo-variant).
使用此布局时，每个子项目都有自己的顶层目录。


The [https://fuchsia.googlesource.com/third_party/llvm-project](https://fuchsia.googlesource.com/third_party/llvm-project)
repository emulates this layout via Git submodules and is updated
automatically by Gerrit. You can use the following command to download
this repository including all the submodules after setting the
`LLVM_MONOREPO` variable.
这个[https://fuchsia.googlesource.com/third_party/llvm-project](https://fuchsia.googlesource.com/third_party/llvm-project)存储库通过Git子模块模拟此布局，并由Gerrit自动更新。设置`LLVM_MONOREPO`变量后，可以使用以下命令下载此存储库，包括所有子模块。


```bash
LLVM_MONOREPO=${HOME}/llvm-project
git clone --recursive https://fuchsia.googlesource.com/third_party/llvm-project ${LLVM_MONOREPO}
```


To update the repository including all the submodules, you can use:
要更新包含所有子模块的存储库，可以使用:


```bash
git pull --recurse-submodules
git submodule update
```


To build the Fuchsia Clang compiler using the monorepo layout, you can
use the following commands:
要使用monorepo布局构建Fuchsia Clang编译器，可以使用以下命令:


```bash
cmake -G Ninja -DLLVM_ENABLE_PROJECTS=clang\;lldb\;lld -DLLVM_ENABLE_RUNTIMES=compiler-rt\;libcxx\;libcxxabi\;libunwind -DFUCHSIA_x86_64_SYSROOT=${FUCHSIA_x86_64_SYSROOT} -DFUCHSIA_aarch64_SYSROOT=${FUCHSIA_aarch64_SYSROOT} -C ${LLVM_MONOREPO}/clang/cmake/caches/Fuchsia.cmake ${LLVM_MONOREPO}/llvm
ninja stage2-distribution
```


Everything else is the same as in the case of standard layout described
above.
其他一切都与上述标准布局的情况相同。


## Developing Clang
开发Clang


When developing Clang, you may want to use a setup that is more suitable for
incremental development and fast turnaround time.
开发Clang时，您可能希望使用更适合增量开发和快速周转时间的设置。


The simplest way to build is to use the following commands:
构建的最简单方法是使用以下命令:


```bash
cmake -G Ninja -DCMAKE_BUILD_TYPE=Debug ${LLVM_SRCDIR}
ninja
```


To also build compiler builtins (a library that provides an implementation of
the low-level target-specific routines) for Fuchsia, you need a few additional
flags:
要为Fuchsia构建编译器内置函数(一个提供低级目标特定例程实现的库)，还需要一些额外的标志:


```bash
-DLLVM_BUILTIN_TARGETS=x86_64-fuchsia-none\;aarch64-fuchsia-none -DBUILTINS_x86_64-fuchsia-none_CMAKE_SYSROOT=${FUCHSIA_x86_64_SYSROOT} -DBUILTINS_x86_64-fuchsia-none_CMAKE_SYSTEM_NAME=Fuchsia -DBUILTINS_aarch64-fuchsia-none_CMAKE_SYSROOT=${FUCHSIA_aarch64_SYSROOT} -DBUILTINS_aarch64-fuchsia-none_CMAKE_SYSTEM_NAME=Fuchsia
```


For this kind of build, the `bin` directory immediate under your main LLVM
build directory contains the compiler binaries.  You can put that directory
into your shell's `PATH`, or use it explicitly in commands, or use it in the
`CLANG_TOOLCHAIN_PREFIX` variable (with trailing slash) for a Zircon build.
对于这种编译，主LLVM编译目录下的“bin”目录包含编译器二进制文件。
您可以将该目录放入shell的“路径”中，或者在命令中显式使用该目录，或者在Zircon生成的“Tang _ TOOLCHAIN _ prefix”变量(带有尾随斜杠)中使用该目录。


Clang is a large project and compiler performance is absolutely critical. To
reduce the build time, we recommend using Clang as a host compiler, and if
possible, LLD as a host linker. These should be ideally built using LTO and
for best possible performance also using Profile-Guided Optimizations (PGO).
clang是一个大型项目，编译器的性能是绝对关键的。
为了缩短构建时间，我们建议使用Clang作为主机编译器，如果可能，还建议使用LLD作为主机链接器。
这些应用程序最好使用LTO构建，并使用配置文件引导优化( PGO )来获得最佳性能。


To set the host compiler, you can use the following extra flags:
要设置主机编译器，可以使用以下额外标志:


```bash
-DCMAKE_C_COMPILER=${CLANG_TOOLCHAIN_PREFIX}clang -DCMAKE_CXX_COMPILER=${CLANG_TOOLCHAIN_PREFIX}clang++ -DLLVM_ENABLE_LLD=ON
```


This assumes that `${CLANG_TOOLCHAIN_PREFIX}` points to the `bin` directory
of a Clang installation, with a trailing slash (as this Make variable is used
in the Zircon build).  For example, to use the compiler from your Fuchsia
checkout (on Linux):
这假定`${CLANG_TOOLCHAIN_PREFIX}`指向Clang安装的“bin”目录，并带有尾随斜杠(因为此Make变量在Zircon生成中使用)。
例如，要使用Fuchsia检验(在Linux上)的编译器:


```bash
CLANG_TOOLCHAIN_PREFIX=${HOME}/fuchsia/buildtools/toolchain/clang+llvm-x86_64-linux/bin/
```


To build both builtins as well as runtimes (C++ library and sanitizer
runtimes), you can also use the cache file, but run only the second
stage, with LTO disabled, which gives you a faster build time suitable even
for incremental development, without having to manually specify all
options:
构建内置和运行时( c++库和杀毒软件运行时)，您也可以使用缓存文件，但只运行第二个阶段，禁用LTO，这为您提供了更快的构建时间，甚至适合增量开发，而无需手动指定所有选项:


```bash
cmake -G Ninja -DCMAKE_BUILD_TYPE=Debug -DCMAKE_C_COMPILER=${CLANG_TOOLCHAIN_PREFIX}clang -DCMAKE_CXX_COMPILER=${CLANG_TOOLCHAIN_PREFIX}clang++ -DLLVM_ENABLE_LTO=OFF -DFUCHSIA_x86_64_SYSROOT=${FUCHSIA_x86_64_SYSROOT} -DFUCHSIA_aarch64_SYSROOT=${FUCHSIA_aarch64_SYSROOT} -C ${LLVM_SRCDIR}/tools/clang/cmake/caches/Fuchsia-stage2.cmake ${LLVM_SRCDIR}
```


## Building sanitized versions of LLVM tools
构建LLVM工具的消毒版本


Most sanitizers can be used on LLVM tools by adding
`LLVM_USE_SANITIZER=<sanitizer name>` to your cmake invocation. MSan is
special however because some llvm tools trigger false positives. To build with
MSan support you first need to build libc++ with MSan support. You can do this
in the same build. To set up a build with MSan support first run CMake with
`LLVM_USE_SANITIZER=Memory` and `LLVM_ENABLE_LIBCXX=ON`.
大多数清理程序可以在LLVM工具上使用，方法是添加
`LLVM_USE_SANITIZER=<sanitizer name>`添加到您的cmake调用中。
但是，MSan是特殊的，因为某些llvm工具会触发误报。
要使用MSan支持进行构建，首先需要使用MSan支持构建libc++。
您可以在同一版本中执行此操作。要使用MSan支持设置生成，请首先运行`LLVM_USE_SANITIZER=Memory`和`LLVM_ENABLE_LIBCXX=ON`。


```bash
cmake -G Ninja -DCMAKE_BUILD_TYPE=Debug -DLLVM_USE_SANITIZER=Memory -DLLVM_ENABLE_LIBCXX=ON -DCMAKE_C_COMPILER=${CLANG_TOOLCHAIN_PREFIX}clang -DCMAKE_CXX_COMPILER=${CLANG_TOOLCHAIN_PREFIX}clang++ -DLLVM_ENABLE_LLD=ON ${LLVM_SRCDIR}
```


Normally you would run Ninja at this point but we want to build everything
using a sanitized version of libc++ but if we build now it will use libc++ from
`${CLANG_TOOLCHAIN_PREFIX}` which isn't sanitized. So first we build just
the cxx and cxxabi targets. These will be used in place of the ones from
`${CLANG_TOOLCHAIN_PREFIX}` when tools dynamically link against libcxx.
通常你会在此时运行Ninja，但是我们希望使用经过清理的libc++版本来构建所有的东西，但是如果我们现在构建，它将使用未经清理的`${CLANG_TOOLCHAIN_PREFIX}`中的libc++版本。
因此，首先我们只构建cxx和cxxabi目标。
这些将用于代替`${CLANG_TOOLCHAIN_PREFIX}`当工具动态链接到libcxx时。


```bash
ninja cxx cxxabi
```


Now that we have a sanitized version of libc++ we can have our build use it
instead of the one from `${CLANG_TOOLCHAIN_PREFIX}` and then build everything.
现在我们有了一个经过清理的libc++版本，我们可以让我们的构建使用它而不是来自`${CLANG_TOOLCHAIN_PREFIX}`的版本，然后构建所有内容。


```bash
ninja
```


Putting that all together:
把这些放在一起:


```bash
cmake -G Ninja -DCMAKE_BUILD_TYPE=Debug -DLLVM_USE_SANITIZER=Address -DLLVM_ENABLE_LIBCXX=ON -DCMAKE_C_COMPILER=${CLANG_TOOLCHAIN_PREFIX}clang -DCMAKE_CXX_COMPILER=${CLANG_TOOLCHAIN_PREFIX}clang++ -DLLVM_ENABLE_LLD=ON ${LLVM_SRCDIR}
ninja libcxx libcxxabi
ninja
```


## Additional Resources
追加资源


Documentation:
文件:
* [Getting Started with the LLVM System](http://llvm.org/docs/GettingStarted.html)
* [Building LLVM with CMake](http://llvm.org/docs/CMake.html)
* [Advanced Build Configurations](http://llvm.org/docs/AdvancedBuilds.html)


Talks:
会谈:
* [2016 LLVM Developers’ Meeting: C. Bieneman "Developing and Shipping LLVM and Clang with CMake"](https://www.youtube.com/watch?v=StF77Cx7pz8)
Build Configurations](http://llvm.org/docs/AdvancedBuilds.html)

Talks:
会谈:
* [2016 LLVM Developers’ Meeting: C. Bieneman "Developing and Shipping LLVM and Clang with CMake"](https://www.youtube.com/watch?v=StF77Cx7pz8)
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTM1MDY4MDIwMiwtMjgyODI0Nzk1XX0=
-->
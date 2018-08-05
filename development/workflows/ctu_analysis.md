# Cross Translation Unit Static Analysis in Zircon
# Zircon 中的 Cross Translation Unit 静态分析

This document describes:

* How to set up cross-translation-unit analysis (**CTU**) with the Clang Static Analyzer (**CSA**) in Zircon;
* The work done by Kareem Khazem during his internship; and
* The remaining work needed to get CTU fully supported on Zircon.

本文档介绍了：

* 如何在 Zircon 中使用 Clang 静态分析器 (**CSA**)设置 cross-translation-unit 分析(**CTU**);
* Kareem Khazem 在实习期间完成的关于 CTU 工作;
* 以及，在Zircon中需要完全支持CTU所做的剩余工作。


## Setting up and running CTU on Zircon
## Zircon 中设置和运行 CTU

**Summary**: Download the source for Clang, and apply several non-mainline patches to it before compiling it. Run my wrapper script around the analysis tools. Download the `CodeChecker` tool; use it to digest the results of the analysis, and start a web server to view the results with a web interface.

**摘要**：下载 Clang 的源代码，并在编译之前应用几个非主线补丁。围绕分析工具运行我的包装脚本。下载 CodeChecker 工具;使用它来理解分析的结果，并启动一个 Web 服务器以通过 Web 界面查看结果。

## CTU-enabling patches
## CTU 授权补丁

There are two patchsets to be aware of:

* The [Samsung](https://github.com/haoNoQ/clang/tree/summary-ipa-draft) patchset, which is an enormous patch adding AST merging support to Clang. It consists mostly of additions to `lib/AST/ASTImporter.cpp`. There is also a (primitive, not working very well) set of tools for CTU analysis under `tools/xtu-build/*`. This patchset is based on an old revision of Clang; this fact, as well as its large size, makes it very difficult to rebase wholesale onto tip-of-tree (**ToT**) Clang.
* The [Ericsson](https://github.com/dkrupp/clang/blob/ctu-master/tools/xtu-build-new/xtu-analyze.py) patchset, which includes a subset of Samsung’s AST merging work and also adds several new tools (`tools/xtu-build-new/*` and `tools/scan-build-py/*`) that allow for CTU analysis. The xtu-build-new tools improve on, and are somewhat different to, Samsung’s xtu-build tools. This patchset is much newer than the Samsung one, and the authors are making an effort to keep it rebased on ToT.

We will be patching Clang with Ericsson’s patchset, since the AST merging work rebases cleanly and we also get the newer analysis tools. However, note that CTU support for Zircon is incomplete; in some cases, the Samsung patchset contains code that provides the required functionality (more details below).

已知的两个补丁集：

* 三星 ([Samsung](https://github.com/haoNoQ/clang/tree/summary-ipa-draft))补丁集，这是一个庞大的补丁，增加了对 Clang 的 AST 合并支持，主要包括对 `lib/AST/ASTImporter.cpp` 的补充。并且在`tools/xtu-build/*`下有整套CTU 分析工具（原始的，运行不太好）。该补丁集基于 Clang 的旧版本和它补丁集庞大的事实，使得它很难在 tip-of-tree (**ToT**) Clang 之上大规模重写。
* 爱立信（[Ericsson](https://github.com/dkrupp/clang/blob/ctu-master/tools/xtu-build-new/xtu-analyze.py)）补丁集，包含了 Samsung 的 AST 合并工作并且加入了一些允许进行 CTU 分析的新工具（`tools/xtu-build-new/*`和 `tools/scan-build-py/*`）。Ericsson 的 xtu-build-new 工具是对 Samsung xtu-build 工具的改进但是又有些区别。该补丁集比 Samsung 的新许多，作者也一直致力于保持它基于 ToT 重写。

我们会采用 Ericsson 的补丁集来修正 Clang 来保证 AST 合并工作能干净的重写，并且我们会成为更新的分析工具。但是，需要注意的是 CTU 对于 Zircon 的支持是不完整的，在某些情况下，Samsung 的补丁集包含了一些提供必需功能的代码（更多详情如下）。

### Steps to build CTU-capable CSA
### 构建 CTU-capable CSA 步骤

1. Download and build Clang and LLVM as usual.
2. In a separate directory, clone Ericsson’s fork of Clang and switch to the ctu-master branch.
3. Download [this script](https://gist.github.com/karkhaz/d11efa611a1bde23490c2773dc0da60d) into Ericsson’s fork and run it. It should dump a series of patches into a patches directory. I purposely only dump the commits from the beginning of Ericsson’s changes until 1bb3636, which was the latest revision during my internship.
    * If you want more up-to-date changes from Ericsson, you can experiment with changing 1bb3636 to HEAD in the script. Make sure to skip commits that merge upstream commits into the ctu-master branch by specifying additional ranges in the script. git log --graph can be helpful to determine what the upstream commits vs. Ericsson’s commits are, I use
    ```
    git log --graph  --decorate --date=relative --format=format:'%C(green)%h%C(yellow) %s%C(reset)%w(0,6,6)%C(bold green)\n%C(cyan)%G? %C(bold red)%aN%C(reset) %cr%C(reset)%w(0,0,0)\n%-D\n%C(reset)' --all
    ```
4. Apply the generated patches to *upstream* Clang (not the Ericsson fork) one at a time.
   ```
   for p in $(ls $PATCH_DIR/*.patch | sort -n); do git am < $p; done
   ```
5. Apply Kareem Khazem’s patches that are listed [below](#zircon-patches) if they haven’t already landed
6. Re-build upstream Clang & LLVM.

译文：

1. 像往常一样下载并构建 Clang 和 LLVM。
2. 在一个单独的目录里，克隆 Ericsson 的 Clang 分支，然后切换到 ctu-master 分支。
3. 将[这个脚本](https://gist.github.com/karkhaz/d11efa611a1bde23490c2773dc0da60d)下载到 Ericsson 的分支中，然后运行，它会将一系列补丁转储到补丁目录中。我特意只转储了 Ericsson 从开始到 1bb3636 的提交 ，这是我在实习期 Ericsson 的最新版本。
    * 如果你想要更多 Ericsson 的最新版的变化，可以尝试在脚本中将 1bb3636 更改为 HEAD。通过在脚本中指定其他范围，确保跳过将上游提交合并到ctu-master分支的提交。使用 git log --graph 可以帮助确定上游提交与爱立信提交的内容
    ```
    git log --graph  --decorate --date=relative --format=format:'%C(green)%h%C(yellow) %s%C(reset)%w(0,6,6)%C(bold green)\n%C(cyan)%G? %C(bold red)%aN%C(reset) %cr%C(reset)%w(0,0,0)\n%-D\n%C(reset)' --all
    ```
4. 将生成的补丁一次一个地应用到 *上游* Clang（不是 Ericsson 分支）。
   ```
   for p in $(ls $PATCH_DIR/*.patch | sort -n); do git am < $p; done
   ```
5. 如果没有Kareem Khazem的补丁，则应用[下面](＃zircon补丁)列出的补丁
6. 重新构建上游Clang和LLVM。



## Running CTU analysis
## 运行 CTU 分析


**Summary:** Run my wrapper script. This builds Zircon normally, then builds it again but dumping serialised ASTs instead of object files, and then finally analyses each file using the dumped ASTs to achieve CTU.

**摘要:** 运行我的包装脚本。这会正常生成Zircon，然后再次构建它，但是会转储序列化的AST而不是目标文件，然后使用转储的AST来分析每个文件以实现CTU。

### How CTU works
### CTU 工作原理

First, the story backwards:

Non-CTU static analysis analyzes the AST of each TU; any function calls to external functions are treated as opaque. Roughly, CTU analysis seeks to *substitute* the opaque function call node with the AST of that function’s implementation.

Thus, a CTU analysis will start analyzing an AST as usual, but when it encounters a function call node, it will try to *merge in* the AST for that function. This relies on the AST for the function already having been serialized to disk beforehand, so that the analyzer can re-load the AST into memory. It also relies on support for AST merging, which is what the Samsung patch to `ASTImporter.cpp` (and the Ericsson patch derived from it) is for.

In order to serialize the ASTs to disk, we need to emulate the real build process. The way to do this is to actually do a real build of Zircon while recording the compiler invocations; this allows us to ‘play back’ the invocations, but with the compiler flags modified to dump AST files rather than object files.

So to summarise, forwards this time:

* Build zircon using Clang, and wrap the build process in a program like [bear](https://github.com/rizsotto/Bear) in order to record compiler invocations and generate a JSON compilation database.
* Replay the same compilation steps, but dumping AST files instead of object files. This is what the [xtu-build.py](https://github.com/dkrupp/clang/blob/ctu-master/tools/xtu-build-new/xtu-build.py) tool does.
* Perform static analysis as usual, but deserialize the AST of every called function when needed. This is what the [xtu-analyze.py](https://github.com/dkrupp/clang/blob/ctu-master/tools/xtu-build-new/xtu-analyze.py) tool does at the top level, by invoking tools in the [scan-build-py/libscanbuild](https://github.com/dkrupp/clang/tree/ctu-master/tools/scan-build-py/libscanbuild) directory through the thin [scan-build replacement](https://github.com/dkrupp/clang/blob/ctu-master/tools/scan-build-py/bin/scan-build) written by the Ericsson team.

These steps are captured in the [Fuchsia wrapper](#fuchsia-wrapper-script) mentioned below. The result of all this is a directory full of reports, in [Apple plist](https://en.wikipedia.org/wiki/Property_list) format, which contain details of reported bugs.

首先，故事倒退：

非CTU静态分析分析每个TU的AST;对外部函数的任何函数调用都被视为不透明。粗略地说，CTU分析试图用该函数实现的AST *代替* 不透明函数调用节点。

因此，CTU分析将像往常一样开始分析一个AST，但是当它遇到一个函数调用节点时，它将尝试 *合并* 该函数的AST。这依赖于AST已预先序列化到磁盘的功能，以便分析器可以将AST重新加载到内存中。它也依赖于对AST合并的支持，这是 Samsung 补丁到`ASTImporter.cpp`（以及 Ericsson 补丁源自它）的补丁。

为了将AST序列化到磁盘上，我们需要模拟真正的构建过程。做到这一点的方法是在记录编译器调用时实际上做一个真正的Zircon版本;这允许我们'回放'调用，但是编译器标志被修改为转储AST文件而不是对象文件。


总而言之，这次转发：

* 使用 Clang 构建 zircon，并将构建过程包装在[bear](https://github.com/rizsotto/Bear)等程序中，以便记录编译器调用并生成 JSON 编译数据库。
* 重播相同的编译步骤，但转储AST文件而不是目标文件。这是[xtu-build.py](https://github.com/dkrupp/clang/blob/ctu-master/tools/xtu-build-new/xtu-build.py)工具的作用。
* Perform static analysis as usual, but deserialize the AST of every called function when needed. This is what the [xtu-analyze.py](https://github.com/dkrupp/clang/blob/ctu-master/tools/xtu-build-new/xtu-analyze.py) tool does at the top level, by invoking tools in the [scan-build-py/libscanbuild](https://github.com/dkrupp/clang/tree/ctu-master/tools/scan-build-py/libscanbuild) directory through the thin [scan-build replacement](https://github.com/dkrupp/clang/blob/ctu-master/tools/scan-build-py/bin/scan-build) written by the Ericsson team.像往常一样执行静态分析，但需要时将每个被调用函数的AST反序列化。
这是[xtu-analyze.py](https://github.com/dkrupp/clang/blob/ctu-master/tools/xtu-build-new/xtu-analyze.py)工具在顶层所做的，通过爱立信团队编写的瘦身版的[scan-build replacement](https://github.com/dkrupp/clang/blob/ctu-master/tools/scan-build-py/bin/scan-build)调用[scan-build-py/libscanbuild](https://github.com/dkrupp/clang/tree/ctu-master/tools/scan-build-py/libscanbuild)目录中的工具。


这些步骤被捕获在下面提到的[Fuchsia包装器](＃fuchsia-wrapper-script)中。所有这些都是一个充满报道的目录，采用[Apple plist](https://en.wikipedia.org/wiki/Property_list)格式，其中包含报告错误的详细信息。


### Ericsson’s wrapper scripts<a name="ericsson-wrapper-script"></a>
### Ericsson 的包装脚本<a name="ericsson-wrapper-script"></a>


There are two sets of tools for running cross-translation-unit analysis:

* The tools under `tools/xtu-build-new` are the top-level scripts. Since the underlying analyzer can fail (i.e. due to the CSA crashing), I’ve patched `xtu-analyze.py` (in Ericsson’s branch) so that it dumps the output of the analyzer (stdout/stderr, not the report) to a file. The output goes in ``$OUT_DIR/{passes,fails}`` depending on the return code of the analyzer, where `$OUT_DIR` is the directory passed to the `-o` argument of `xtu-analyze.py`. The particularly helpful part of those files is the *second line* that starts with `analyze: DEBUG: exec command in`, which is emitted by the `libscanbuild` tools (next bullet point). That command is the actual invocation to the CSA after the long, tedious process of modifying its command line. Therefore, it’s the command that you will need if you want to run the CSA on a troublesome file using gdb.
* The tools under `tools/scan-build-py` are a bird’s nest of tools to wrap around the actual invocation to Clang. They are responsible for modifying the command line. I’m not too familiar with them, and haven’t had to interfere with them in the past.

有两组工具用于运行 cross-translation-unit 分析:

* `tools / xtu-build-new`下的工具是顶级脚本。由于底层分析器可能会失败（例如，由于CSA崩溃），所以我修补了`xtu-analysis.py`（在 Ericsson 分支中），以便将分析器的输出（stdout / stderr，而不是报告）转储为一份文件。根据分析器的返回码，输出进入``OUT_DIR / {passes，failed}``，其中`$ OUT_DIR`是传递给`xtu-analyze.py`的`-o`参数的目录。这些文件中特别有用的部分是*第二行*，它由`libscanbuild`工具（下一个项目符号点）发出的`analyze：DEBUG：exec command in`开头。该命令是修改其命令行的漫长而单调的过程之后实际调用CSA的方式。因此，如果您想使用gdb在一个麻烦的文件上运行CSA，则需要使用该命令。
* `tools / scan-build-py`下的工具是一个工具包，用于包装实际调用Clang。他们负责修改命令行。我对他们不太熟悉，过去也不必干涉他们。


### Fuchsia wrapper script<a name="fuchsia-wrapper-script"></a>
### Fuchsia 包装脚本<a name="fuchsia-wrapper-script"></a>

[This very small shell script](https://gist.github.com/karkhaz/c8ded50e564d73853731266fec729454) wraps the Ericsson `xtu-build-new` wrappers. To do a complete analysis of Zircon, make sure to clean first, and specify the correct path to your build of Clang. Then, in the zircon directory:

```
make clean && ./run.sh
```

In order to build only the kernel, specify a `TARGET` as an environment variable:

```
make clean && TARGET=./build-zircon-pc-x86-64/zircon.elf ./run.sh
```

The script also requires [clangify.py](https://gist.github.com/karkhaz/2ab5e8c7a8783318d44ceca715f20438) to be in the zircon directory with executable bit set. After the analysis has finished, there will be a `.result-xtu` directory, containing:

* A bunch of Apple plist files, which are the bug reports;
* A fails directory, containing the std{out,err} of analyzer invocations that returned non-zero;
* A passes directory, containing the std{out,err} of analyzer invocations that returned 0.

[这个非常小的 shell 脚本](https://gist.github.com/karkhaz/c8ded50e564d73853731266fec729454) 封装了 Ericsson `xtu-build-new` 封装。要对 zircon 进行完整分析，请确认先清理，然后指定正确的路径去构建 Clang。 然后，在 zircon 目录中：

```
make clean && ./run.sh
```

为了只构建内核，指定 `TARGET` 作为环境变量:

```
make clean && TARGET=./build-zircon-pc-x86-64/zircon.elf ./run.sh
```

该脚本还需要 [clangify.py](https://gist.github.com/karkhaz/2ab5e8c7a8783318d44ceca715f20438) 位于可执行位设置的 zircon 目录中。分析完成后，会有一个 `.result-xtu` 包含了:

* 一堆报告 BUG 的苹果 plist 文件;
* A fails directory, containing the std{out,err} of analyzer invocations that returned non-zero;一个失败目录，包含了分析器的 std{out,err} 返回非零的调用;
* A passes directory, containing the std{out,err} of analyzer invocations that returned 0.一个成功目录，包含了分析器的 std{out,err} 返回零的调用。

## Viewing analysis results
## 查看分析结果


At the moment, the only way of parsing the plist reports and viewing them with a web interface is by using the [CodeChecker](https://github.com/Ericsson/codechecker) tool, which is developed at Ericsson and used for code comprehension and many other tasks. CodeChecker needs a large number of dependencies installed, and it’s best to install them with **pip** or **npm** or whatever rather than using **apt-get**. In short, after performing the analysis and dumping the plists into .result-xtu, you can invoke `CodeChecker plist` to parse the plists:

```
CodeChecker plist -d .result-xtu -n 2016-12-12T21:47_uniq_name -j 48
```

The argument to `-n` needs to be unique on each invocation of `CodeChecker plist`, as it represents a single parse run. CodeChecker complains otherwise. Then, run `CodeChecker server` to start a webserver on `localhost:8001`, which will display the reports of all previous parsing runs.


此时，只有一种方法来解析 plist 报告文件，并且将他们以 web 接口的形式展示,这种方法就是使用 [CodeChecker](https://github.com/Ericsson/codechecker) 工具。该工具是 Ericsson 开发的，用于代码阅读和其他任务的。CodeChecker 需要大量的安装，最好使用 **pip** 或 **npm** 或者其他管理工具来安装，而不是 **apt-get** 。总之，执行分析并将 plist 转储到 .result-xtu 之后，你可以调用 `CodeChecker plist` 来解析 plist 文件:

```
CodeChecker plist -d .result-xtu -n 2016-12-12T21:47_uniq_name -j 48
```

在 `CodeChecker plist` 的每次调用中，`-n` 的参数都必须是唯一的，因为它代表一个单独的分析运行。否则，CodeChecker 会抱怨。然后运行 `CodeChecker server` 在 `localhost:8001` 上启动一个 web 服务器，它会显示所有之前的分析运行报告。

## Getting Help
## 获得帮助

The Samsung patchset was authored by [Aleksei Sidorin](mailto:a.sidorin@samsung.com) and his team. Aleksei is quite knowledgeable about `ASTImporter.cpp` and other AST merging aspects, and was very helpful. He and [Sean Callanan](mailto:scallanan@apple.com) were happy to review my AST Importer patches. Aleksei also [gave a relevant talk](https://www.youtube.com/watch?v=jbLkZ82mYE4) about summary-based interprocedural analysis at the 2016 LLVM Developers Meeting.

The Ericsson patchset was authored by [Gábor Horváth](mailto:xazax.hun@gmail.com) and his team. Gábor was very helpful with advice on how to run CTU analysis with the `xtu-build-new` tools.

I ([Kareem Khazem](mailto:karkhaz@karkhaz.com)) am also happy to help out where I can.

The LLVM irc channel can also be helpful.


Samsung 补丁集由 [Aleksei Sidorin](mailto：a.sidorin@samsung.com) 和他的团队撰写。 Aleksei 对 `ASTImporter.cpp` 和其他AST合并方面非常了解，并且非常有帮助。他和 [Sean Callanan](mailto：scallanan@apple.com) 很乐意检查我的AST Importer补丁。 Aleksei 还在2016年 LLVM 开发者大会上发表了关于基于概要的过程间分析的[相关讨论](https://www.youtube.com/watch?v=jbLkZ82mYE4)。

Ericsson 补丁集由 [Gábor Horváth](mailto:xazax.hun@gmail.com) 和他的团队撰写。Gábor 对于如何使用 `xtu-build-new` 工具运行 CTU 分析的建议很有帮助。 

我([Kareem Khazem](mailto:karkhaz@karkhaz.com))也很乐意进我所能帮帮忙。

LLVM irc频道也会有所帮助。


## Zircon-specific analyses
## Zircon特性分析

Upstream Clang has been very receptive to receiving patches for Zircon-specific Clang checkers. The [MutexInInterruptContext](https://reviews.llvm.org/D27854) checker is one example (ported from an LLVM pass written by Farid Molazem Tabrizi), as are the [SpinLockChecker](https://reviews.llvm.org/D26340) and [MutexChecker](https://reviews.llvm.org/D26342). Potential reviewers for Clang checks are Devin Coughlin (from Apple), Artem Dergachev (on Aleksei Sidorin’s team at Samsung) and Anna Zaks (also at Apple).

These checkers are typically *opt-in*, meaning that you need to pass a flag to the analyzer to enable them: something like `-analyzer-checker=optin.zircon.MutexInInterruptContext`.

If those patches haven’t landed in Clang, you will need to apply them. To use them for analyzing Zircon with the [Ericsson wrapper scripts](#ericsson-wrapper-script), you should modify the [Fuchsia wrapper script](#fuchsia-wrapper-script) by adding the option `-e optin.zircon.MutexInInterruptContext` to the invocation of `xtu-analyze.py` at the end of the file. The patch for `MutexInInterruptContext` has a test suite, which can be used as an example of what the analysis is capable of.


Upstream Clang 已经非常愿意接受 Zircon 专用 Clang 语法检查程序。[MutexInInterruptContext](https://reviews.llvm.org/D27854) 检查器就是一个例子（由 Farid Molazem Tabrizi 编写的 LLVM 通道移植而来），[SpinLockChecker](https://reviews.llvm.org/D26340) 和 [MutexChecker](https://reviews.llvm.org/D26342) 也是如此。 Clang 核实的潜在评论者是 Devin Coughlin（来自 Apple），Artem Dergachev（在三星的 Aleksei Sidorin 团队）和 Anna Zaks（也在Apple）。


这些检查器通常是 *opt-in*，这意味着你需要将一个标志传递给分析器以启用它们：类似于 `-analyzer-checker=optin.zircon.MutexInInterruptContext` 。


如果 Clang 上没有这些补丁，你需要申请它们。要使用它们和 [Ericsson 包装脚本](#ericsson-wrapper-script) 分析 Zircon，你应该通过给调用文件末尾的 `xtu-analyze.py` 添加选项 `-e optin.zircon.MutexInInterruptContext`  来修改[Fuchsia包装脚本](＃fuchsia-wrapper脚本)。 `MutexInInterruptContext`的补丁有一个测试套件，可以用来作为分析能力的一个例子。

# Progress on CTU support in Zircon
# Zircon 支持的 CTU 发展历程

## Problems fixed in the AST importer
## AST 导入器已确定的问题

The upstream CSA crashes on the vast majority of Zircon files. This section describes some of the problems that Kareem Khazem encountered and their fixes.


上游 CSA 在绝大多数 Zircon 文件上崩溃。本节介绍 Kareem Khazem 遇到的一些问题及其修复。

### Unsupported AST Nodes<a name="zircon-patches"></a>
### 不支持的 AST 节点<a name="zircon-patches"></a>


The Clang Static Analyzer is unable to import a lot of Zircon code, due to not having implemented support for importing certain kinds of AST nodes. Patches to support these nodes are listed here:

<table>
  <tr>
    <td>AtomicType</td>
    <td>Patch merged into upstream</td>
  </tr>
  <tr>
    <td>CXXDependentScopeMemberExpr</td>
    <td>https://reviews.llvm.org/D26904</td>
  </tr>
  <tr>
    <td>UnresolvedLookupExpr</td>
    <td>https://reviews.llvm.org/D27033</td>
  </tr>
  <tr>
    <td>DependentSizedArray</td>
    <td></td>
  </tr>
  <tr>
    <td>CXXUnresolvedConstructExpr</td>
    <td></td>
  </tr>
  <tr>
    <td>UsingDecl</td>
    <td>https://reviews.llvm.org/D27181</td>
  </tr>
  <tr>
    <td>UsingShadowDecl</td>
    <td>https://reviews.llvm.org/D27181</td>
  </tr>
  <tr>
    <td>FunctionTemplateDecl</td>
    <td>https://reviews.llvm.org/D26904</td>
  </tr>
</table>


In general, when implementing support for new node types, one must implement a `VisitNode` function in `ASTImporter.cpp`, and also unit tests and functional tests; Kareem’s patches above contain examples. There are still quite a few unsupported AST nodes remaining; grep the analyzer output directory for `error: cannot import unsupported AST node`.

The Ericsson patchset contains only a subset of the `ASTImporter` code in the Samsung patchset. In some cases, the `Visit` function for an unsupported node can be taken straight from the Samsung patchset. However, the Samsung patchset does not include any tests, so it will still be necessary to write tests before the support for that node is upstreamed.


Clang 静态分析器无法导入很多 Zircon 代码，因为没有实现对导入某些类型的 AST 节点的支持。这里列出了支持这些节点的补丁：

<table>
  <tr>
    <td>AtomicType</td>
    <td>Patch merged into upstream</td>
  </tr>
  <tr>
    <td>CXXDependentScopeMemberExpr</td>
    <td>https://reviews.llvm.org/D26904</td>
  </tr>
  <tr>
    <td>UnresolvedLookupExpr</td>
    <td>https://reviews.llvm.org/D27033</td>
  </tr>
  <tr>
    <td>DependentSizedArray</td>
    <td></td>
  </tr>
  <tr>
    <td>CXXUnresolvedConstructExpr</td>
    <td></td>
  </tr>
  <tr>
    <td>UsingDecl</td>
    <td>https://reviews.llvm.org/D27181</td>
  </tr>
  <tr>
    <td>UsingShadowDecl</td>
    <td>https://reviews.llvm.org/D27181</td>
  </tr>
  <tr>
    <td>FunctionTemplateDecl</td>
    <td>https://reviews.llvm.org/D26904</td>
  </tr>
</table>


一般来说，在实现对新节点类型的支持时，必须在 `ASTImporter.cpp` 中实现 `VisitNode` 函数，还要进行单元测试和功能测试;以上 Kareem 的补丁包含示例。仍有不少不支持的 AST 节点; 在分析器输出目录中查询 `error: cannot import unsupported AST node`。

Ericsson 补丁集仅包含 Samsung 补丁集中的一部分 `ASTImporter`代码。在某些情况下，可以直接从 Samsung 补丁集中获取不受支持节点的 `Visit`功能。但是，Samsung 补丁集不包含任何测试，所以在对该节点的支持进行升级之前，仍然有必要编写测试。


### Segfaults galore
### 大量的错误

A lot of the code in `ASTImporter.cpp` is buggy. Sometimes Aleksei has private patches for issues, like [this one](https://reviews.llvm.org/D26753), so it’s worth giving him (**a-sid**) a quick ping on IRC. My strategy for debugging is to look through the wrapper output for the *second* string starting with `analyze: DEBUG: exec command in` (followed by the actual command line of the analyzer), and running that command line through gdb. It often takes only a few hours to track down where a segfault is coming from.


`ASTImporter.cpp` 中的很多代码都是有 bug 的。有时候 Aleksei 有问题的私人补丁，如[this one](https://reviews.llvm.org/D26753)，所以值得给他(**a-sid**）在IRC上快速ping一下。我的调试策略是以  `analyze: DEBUG: exec command in`（后面跟着分析器的实际命令行）为开头，并通过gdb运行该命令行，查看 *second* 字符串的包装器输出。通常只需几个小时就可以找出段错误来自何处。

## Bugs found before and after CTU
## CTU之前和之后发现的错误

### Possible bug in VFS?
### VFS中可能的错误？


This is a double-free of `oldparent`, which is declared uninitialized on `system/ulib/fs/vfs.c:vfs_rename`. Two lines later, `vfs_walk` (same file) is called with `oldparent` as its second argument. It is possible to return from `vfs_walk` without assigning to `oldparent` by entering the for loop and hitting the `return r` statement on the first loop. If the value of `r` is greater than zero, then we go to the `else if` statement, which calls `vn_release` on `oldparent` (which is still uninitialized).


这是 `oldparent` 的双重释放，它在 `system / ulib / fs / vfs.c：vfs_rename` 上声明为未初始化。两行后，`vfs_walk`（同一个文件）被调用并以`oldparent`作为其第二个参数。通过在第一个循环中输入for循环并敲击 `return r` 语句，可以从`vfs_walk`返回而不分配给`oldparent`。如果 `r` 的值大于零，那么我们转到 `else if` 语句，该语句在`oldparent`（它仍然未初始化）上调用`vn_release`。

### Possible bug in thread?
### 线程中可能的错误？


This is a use-after-free. The path is:

* `kernel/kernel/thread.c:thread_detach_and_resume`
    * Call `thread_detach(t)`
        * Return `thread_join(t, NULL, 0)`
            * free `t` and return `NO_ERROR`
        * Return `NO_ERROR`
    * Check for error is 1false1
    * Call `thread_resume(t)`, which has been freed.
        * `thread_resume` then accesses `t`’s fields.

这是免费使用的。路径是：

* `kernel/kernel/thread.c:thread_detach_and_resume`
    * 调用 `thread_detach(t)`
        * 返回 `thread_join(t, NULL, 0)`
            * 释放 `t` 并且返回 `NO_ERROR`
        * 返回 `NO_ERROR`
    * 检查错误是 1false1
    * 调用已被释放的 `thread_resume(t)`.
        * `thread_resume`然后访问 `t` 的字段.

## CTU false positives
## CTU误报

* The CSA cannot resolve the implementation of functions that are called through function pointers. This means that it cannot make any assumptions about what the return value of the function might be, nor any effects that the function might have on output parameters.
* There are several classes of function whose implementations are not accessible to the analyzer. Again, the analyzer cannot know that such functions touch their output arguments, so they will spuriously report that the following code reads from a garbage value:
  ```
  struct timeval tv;
  gettimeofday(&tv, NULL);
  printf("%d\n", tv.tv_usec);   // [SPURIOUS REPORT] Access to
                                // uninitialized variable ‘tv’
  ```
* Some kinds of functions that are vulnerable to this kind of imprecision include:
    * System calls (like `gettimeofday`)
    * Compiler builtins (like `memcpy`)

* CSA 无法解析通过函数指针调用的函数的实现。这意味着它不能对函数的返回值做出任何假设，也不能对函数可能对输出参数产生的影响作出任何假设。
* 有几个功能类的分析仪无法访问其实现。同样，分析器不知道这些函数会触及它们的输出参数，所以它们会虚假地报告下面的代码从垃圾值中读取：
  ```
  struct timeval tv;
  gettimeofday(&tv, NULL);
  printf("%d\n", tv.tv_usec);   // [SPURIOUS REPORT] Access to
                                // uninitialized variable ‘tv’
  ```
* 一些易受此类不精确影响的功能包括：
    * 系统调用 (例如 `gettimeofday`)
    * 编译器内置 (例如 `memcpy`)
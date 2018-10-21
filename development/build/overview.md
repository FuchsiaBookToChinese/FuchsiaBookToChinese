# 构建系统

## 概览

Fuchsia 构建系统旨在为不同设备构建启动镜像和可安装包文件。为了实现这个目标，构建系统使用了
[GN][gn-main]（一个元构建系统，用于生成可供 [Ninja][ninja-main] 使用的文件）来执行实际的
构建操作。关于 GN 的介绍请参看：[使用 GN 构建][gn-preso]。


需要注意的是，Zircon内核的构建使用了另外一种完全不同的构建系统，该系统基于GNU Make。
其余部分的构建基于预先构建完成的Zircon进行。

## Products

构建生成的镜像文件，其内容是由一系列顶层 products 来控制的。Products 定义了很多组包文件，
这些包文件可以被包含在启动镜像和系统更新镜像中，可以被预装在 Paver 镜像中，也可以通过更新系统
进行安装。[products][products.md] 文档记录了 Product 定义中不同字段的结构和使用方式。

## 包 （Packages）

Products 是由包构成的，这些包可能聚合或者引用了其它待构建的包或者 GN 标签。关于包的文档请
参阅：[包][packages.md]。

## 构建目标

构建目标在代码树中随处可见，它们在 `BUILD.gn` 文件中被定义。
这些文件使用了一种类似 Python 的句法结构来声明了可构建的对象：

``` py
import("//build/some/template.gni")

my_template("foo") {
  name = "foo"
  extra_options = "//my/foo/options"
  deps = [
    "//some/random/framework",
    "//some/other/random/framework",
  ]
}
```

GN 可用命令（即可以由 GN CLI 执行的命令）以及结构（内建的目标对象声明类型）在[GN 参考文档][gn-reference]
中有详细介绍。在[`//build` 项目][build-project]的 `.gni` 文件中也有很多关于自定义模版的内容

这些自定义模版大多定义了自定义的目标对象声明类型，比如[包声明类型][packages-source]。

> TODO(pylaligand): 列举可用的模版

## 执行构建

最简单的构建方式是参照 [Getting Started](/getting_started.md#Setup-Build-Environment)
文档使用辅助工具 `fx` 进行构建。下文介绍了 `fx` 的具体操作：

### A

第一步是构建Zircon内核。这里我们要使用它自己的构建系统：

```bash
$ scripts/build-zircon.sh
```

这就是命令 `fx build-zircon` 具体执行的内容。

执行 `build-zircon.sh -h` 命令可以查看全部可用选项。
Zircon的 [Getting started][zircon-getting-started] 和
 [Makefile options][zircon-makefile-options] 文档中对此有更加详细的描述。

### B

接下来配置待生成镜像中的内容。这里我们需要选择要被构建入镜像的顶层 product：

```
# fuchsia_base is typically "default".
# my_stuff is a possibly-empty list of extra packages to preinstall.

$ buildtools/gn gen out/x64 --args='fuchsia_products=["garnet/products/fuchsia_base"] fuchsia_packages=["build/gn/my_stuff"]'
```

这一步操作将会生成一个`out/x64`目录，该目录中包含了 Ninja 文件。

这一步操作是命令 `fx set` 指令具体进行的操作。

执行 `buildtools/gn args out/x64 --list` 命令可以查看全部 GN 构建参数。
文档 [Variants](variants.md) 中有对于选项 `select_variant` 的介绍。

### C

最后一步是使用 Ninja 进行实际意义上的构建操作：

```
$ buildtools/ninja -C out/<arch> -j 64
```

命令 `fx build` 负责了这一步的内容。

## 重新构建

### 修改非 Zircon 文件后的重新构建

在修改某些源文件后重新构建代码树只需要重新执行步骤 **C**。即使你修改了 `BUILD.gn` 文件你也只需要
做这一步操作，因为当你修改构建文件时，GN 会添加 Ninja 目标对象以更新 Ninja 目标对象。对于那些
用来配置构建的包文件，这一条也同样适用。

### 修改了 Zircon 文件后的重新构建

你需要重新执行步骤 **A** 和步骤 **C**。

### 同步源码之后的重新构建


如果 Zircon 代码树中的内容有过改动，那么你很可能需要再次执行步骤 A。在这之后，再执行步骤 C 即可。

## Tips 和 tricks

### 检查 product 中的所有包

```bash
$ build/gn/preprocess_products.py --products '["garnet/products/default"]'
```

### 可视化构建包的层级结构

```bash
$ scripts/visualize_module_tree.py > tree.dot
$ dot -Tpng tree.dot -o tree.png
```

### 检查 GN 目标对象的内容

```bash
$ buildtools/gn desc out/x64 //path/to/my:target
```

### 搜索对某一个 GN 目标对象的引用

```bash
$ buildtools/gn refs out/x64 //path/to/my:target
```

### 为编译平台指定目标

不同的平台工具（有一些被构建本身所使用）需要和最终镜像一起构建出来。

若要从模块文件中指定平台工具链的构建目标，请执行如下语句：

```
//path/to/target(//build/toolchain:host_x64)
```

若要从 `BUILD.gn.n` 文件中指定平台工具链的构建目标，请执行如下语句：

```
//path/to/target($host_toolchain)
```

### 构建指定的目标对象

如果一个目标对象在 GN 构建文件中被定义为 `//foo/bar/blah:dash`，那我们可以使用以下指令
构建这个目标对象以及它所有的依赖文件：

```bash

$ buildtools/ninja -C out/x64 -j64 foo/bar/blah:dash
```

需要注意的是这个操作仅对于默认工具链中的目标对象生效。

### 探索 Ninja 目标对象

GN 对由它产生的 Ninja 目标对象进行了完善的记录。可以用以下命令访问相应文档： 

```bash
$ buildtools/gn help ninja_rules
```

你也可以使用以下命令来浏览定义在当前输出文件夹中的 Ninja 目标集：

```bash
$ buildtools/ninja -C out/x64 -t browse
```

需要注意的是，Ninja 目标对象的存在并不意味着该目标会被构建，为了使该目标对象被正常构建，
该目标对象需要依赖于 “default” 目标对象。

### 探究Ninja做出某种操作的原因

在 Ninja 指令中附加 `-d explain` 指令可以让 Ninja 为你解释它每一步的执行细节。

### 调试构建过程中出现的问题

当你运行构建时，Ninja 会维护一个日志文件。这个日志文件可用于生成可视化的构建流程。

1. 删除 output 目录 —— 确保日志文件仅记录了你接下来想要执行的构建信息；
2. 执行通常情况下的构建；
3. 从 <https://github.com/nico/ninjatracing> 获取辅助工具；
4. 执行命令 `ninjatracing <output directory>/.ninja_log > trace.json`；
5.  在 chrome 中使用 `about:tracing` 加载生成的 json 文件。

## 故障排除

### 我的 GN 目标对象没有被构建！

请确认该目标对象的标签是否在模块文件中被正确定义，如果没有，构建系统将会忽略此目标对象。

### GN 提示缺失 `sysroot`

你可能在执行步骤 **B** 之前忘记了执行步骤 **A**。

> TODO(pylaligand): command showing path to default target
> TODO(pylaligand): 命令显示默认目标对象的路径

### GN 内部设置

> TODO(pylaligand): .gn, default target, mkbootfs, GN labels insertion

[gn-main]: https://chromium.googlesource.com/chromium/src/tools/gn/+/HEAD/README.md
[gn-preso]: https://docs.google.com/presentation/d/15Zwb53JcncHfEwHpnG_PoIbbzQ3GQi_cpujYwbpcbZo/
[ninja-main]: https://ninja-build.org/
[gn-reference]: https://gn.googlesource.com/gn/+/master/docs/reference.md
[build-project]: https://fuchsia.googlesource.com/build/+/master/
[zircon-getting-started]: https://fuchsia.googlesource.com/zircon/+/master/docs/getting_started.md
[zircon-makefile-options]: https://fuchsia.googlesource.com/zircon/+/master/docs/makefile_options.md

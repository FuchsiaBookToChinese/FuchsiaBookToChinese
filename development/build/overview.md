# The build system

# 构建系统

## Overview

## 概览

The Fuchsia build system aims at building both boot images and installable
packages for various devices. To do so, it uses [GN][gn-main], a meta-build
system that generates build files consumed by [Ninja][ninja-main], which
executes the actual build. [Using GN build][gn-preso] is a good intro to GN.

Fuchsia构建系统旨在为不同设备构建启动镜像和可安装包文件。为了实现这个目标，构建系统使用了
[GN][gn-main]（一个元构建系统，用于生成可供[Ninja][ninja-main]使用的文件）来执行实际的
构建操作。关于GN的介绍请参看：[使用GN构建][gn-preso]。

Note that Zircon uses an entirely different build system based on GNU Make.
The rest of the build relies on Zircon being built ahead of time.

需要注意的是，Zircon内核的构建使用了另外一种完全不同的构建系统，该系统基于GNU Make。
其余部分的构建基于预先构建完成的Zircon进行。

## Products

## Products

The contents of the generated image are controlled by a set of top level
products. Products define sets of packages that are included in boot and
system update images, preinstalled in paver images, and installable using the
update system. [products](products.md) documents the structure and usage of
fields in product definitions.

构建生成出的镜像文件，其内容是由一系列顶层products来控制的。Products定义了很多组包文件，
这些包文件可以被包含在启动镜像和系统更新镜像中，可以被预装在Paver镜像中，也可以通过更新系统
进行安装。[products][products.md]记录了Product定义中不同字段的结构和使用方式。

## Packages

## 包 （Packages）

The contents of products are packages, which may aggregate or reference other
packages and GN labels that are to be built. See [packages](packages.md)
for more information.

Products是由包构成的，这些包可能聚合或者引用了其他待构建的包或者GN标签。关于包的文档请
参看：[包][packages.md]。

## Build targets

## 构建目标

Build targets are defined in `BUILD.gn` files scattered all over the source
tree. These files use a Python-like syntax to declare buildable objects:

构建目标在代码树中随处可见，它们在`BUILD.gn`文件中被定义。
这些文件使用了一种类似Python的句法结构来声明了可构建的对象：
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

Available commands (invoked using gn cli tool) and constructs (built-in target
declaration types) are defined in the [GN reference][gn-reference]. There are
also a handful of custom templates in `.gni` files in the
[`//build` project][build-project].

GN相关命令（即可以由GN CLI执行的命令）以及结构（内建的目标对象声明类型）在[GN文档][gn-reference]
中有详细介绍。在[`//build`项目][build-project]的`.gni`文件中也有很多关于自定义模版的内容

These custom templates mostly define custom target declaration types, such as
the [package declaration type][packages-source].

这些自定义模版大多定义了自定义的目标对象声明类型，比如[包声明类型][packages-source]。

> TODO(pylaligand): list available templates

TODO：列举可用的模版

## Executing a build

## 执行构建

The simplest way to this is through the `fx` tool, as described in
[Getting Started](/getting_started.md#Setup-Build-Environment). Read on to see
what `fx` does under the hood.

最简单的开始构建的方式是依照[Getting Started](/getting_started.md#Setup-Build-Environment)
文档通过辅助工具`fx`进行构建。下文介绍了`fx`的具体操作：

### A

The first step is to build Zircon which uses its own build system:

第一步是构建Zircon内核。这里我们要使用它自己的构建系统：

```bash
$ scripts/build-zircon.sh
```

This is what gets run under the hood by `fx build-zircon`.

这就是命令`fx build-zircon`具体执行的内容。

For a list of all options, run `build-zircon.sh -h`. See Zircon's
[Getting started][zircon-getting-started] and
[Makefile options][zircon-makefile-options] for details.

执行`build-zircon.sh -h`命令可以查看全部可用选项。
Zircon的[Getting started][zircon-getting-started]和
[Makefile options][zircon-makefile-options]文档中对此有更加详细的描述。

### B

Then configure the content of the generated image by choosing the top level
product to build:

接下来配置待生成镜像中的内容。这里我们需要选择要被构建入镜像的顶层product：

```
# fuchsia_base is typically "default".
# my_stuff is a possibly-empty list of extra packages to preinstall.

$ buildtools/gn gen out/x64 --args='fuchsia_products=["garnet/products/fuchsia_base"] fuchsia_packages=["build/gn/my_stuff"]'
```
This will create an `out/x64` directory containing Ninja files.

这一步操作将会生成一个`out/x64`目录，该目录中有Ninja文件。

This is what gets run under the hood by `fx set`.

这一步则是命令`fx set`具体进行的操作。

For a list of all GN build arguments, run `buildtools/gn args out/x64 --list`.
For documentation on the `select_variant` argument, see [Variants](variants.md).

执行`buildtools/gn args out/x64 --list`命令可以查看全部GN构建参数。
文档[Variants](variants.md)中有对于选项`select_variant`的介绍。

### C

The final step is to run the actual build with Ninja:

最后一步是使用Ninja进行实际意义上的构建操作：

```
$ buildtools/ninja -C out/<arch> -j 64
```

This is what gets run under the hood by `fx build`.

命令`fx build`负责了这一步的内容。

## Rebuilding

## 重新构建

### After modifying non-Zircon files

### 修改非Zircon文件后的重新构建

In order to rebuild the tree after modifying some sources, just rerun step
**C**. This holds true even if you modify `BUILD.gn` files as GN adds Ninja
targets to update Ninja targets if build files are changed! The same holds true
for package files used to configure the build.

在修改某些源文件后重新构建代码树只需要重新执行步骤 **C**。即使你修改了`BUILD.gn`文件你也只需要
做这一步操作，因为当你修改构建文件时，GN会添加Ninja目标对象以更新Ninja目标对象。对于那些
用来配置构建的包文件，这一条也同样适用。

### After modifying Zircon files

### 修改了Zircon文件后的重新构建

You will want to rerun **A** and **C**.

你需要重新执行步骤 **A** 和步骤 **C**。

### After syncing sources

### 同步源码之后的重新构建

You’ll most likely need to run **A** once if anything in the Zircon tree was
changed. After that, run **C** again.

如果Zircon代码树中的内容有过改动，那么你很可能需要再次执行步骤A。之后执行步骤C即可。

## Tips and tricks

## Tips和tricks

## Inspecting all packages in a product

### 检查product中的所有包

```bash
$ build/gn/preprocess_products.py --products '["garnet/products/default"]'
```

### Visualizing the hierarchy of build packages

### 可视化构建包的层级结构

```bash
$ scripts/visualize_module_tree.py > tree.dot
$ dot -Tpng tree.dot -o tree.png
```

### Inspecting the content of a GN target

### 检查GN目标对象的内容

```bash
$ buildtools/gn desc out/x64 //path/to/my:target
```

### Finding references to a GN target

### 搜索对某一个GN目标对象的引用

```bash
$ buildtools/gn refs out/x64 //path/to/my:target
```

### Referencing targets for the build host

### 为目标对象指定编译平台

Various host tools (some used in the build itself) need to be built along with
the final image.

不同的平台工具（有一些被构建本身所使用）需要和最终镜像一起构建出来。

To reference a build target for the host toolchain from a module file:

使用以下语法，可以为目标对象指定使用某个来源于模块文件的平台工具链：

```
//path/to/target(//build/toolchain:host_x64)
```

To reference a build target for the host toolchain from within a `BUILD.gn`
file:
使用以下语法，可以为目标对象指定使用某个来源于`BUILD.gn`内部的平台工具链：
```
//path/to/target($host_toolchain)
```

### Building only a specific target

### 构建指定的目标对象

If a target is defined in a GN build file as `//foo/bar/blah:dash`, that target
(and its dependencies) can be built with:

如果一个目标对象在GN构建文件中被定义为`//foo/bar/blah:dash`，那我们可以使用以下指令
构建这个目标对象以及它所有的依赖文件：

```bash

$ buildtools/ninja -C out/x64 -j64 foo/bar/blah:dash
```
Note that this only works for targets in the default toolchain.

需要注意的是这个操作仅对于默认工具链中的目标对象生效。

### Exploring Ninja targets

### 探索Ninja目标对象

GN extensively documents which Ninja targets it generates. The documentation is
accessible with:

GN为由它产生的Ninja目标对象生成了完善的文档。可以用以下命令访问该文档： 

```bash
$ buildtools/gn help ninja_rules
```
You can also browse the set of Ninja targets currently defined in your output
directory with:

你也可以使用以下命令来浏览当前定义在输出文件夹中的Ninja目标对象：
```bash
$ buildtools/ninja -C out/x64 -t browse
```
Note that the presence of a Ninja target does not mean it will be built - for
that it needs to depend on the “default” target.

需要注意的是，Ninja目标对象的存在并不意味着该目标会被构建，为了使该目标对象被正常构建，
该目标对象需要依赖于“default”目标对象。

### Understanding why Ninja does what it does

### 探究Ninja做出某种操作的原因

Add `-d explain` to your Ninja command to have it explain every step of its
execution.

在Ninja指令中使用`-d explain`可以让Ninja为你解释它每一步的操作。

### Debugging build timing issues

### 调试构建过程中出现的问题

When running a build, Ninja keeps logs that can be used to generate
visualizations of the build process:

当你运行构建时，Ninja会维护一个日志文件。这个日志文件可用于可视化完整的构建流程。

1. Delete your output directory - this is to ensure the logs represent only the
   build iteration you’re about to run;
1. Run a build as you would normally do;
1. Get <https://github.com/nico/ninjatracing>;
1. Run `ninjatracing <output directory>/.ninja_log > trace.json`;
1. Load the resulting json file in Chrome in `about:tracing`.

1. 删除output目录 —— 确保日志文件仅记录了你接下来想要执行的构建信息；
1. 执行构建；
1. 从<https://github.com/nico/ninjatracing>获取辅助工具；
1. 执行命令 `ninjatracing <output directory>/.ninja_log > trace.json`；
1. 在chrome中使用`about:tracing`加载生成的json文件。

## Troubleshooting

### My GN target is not being built!

Make sure it rolls up to a label defined in a module file, otherwise the build
system will ignore it.

请确认该目标对象的标签是否在模块文件中被正确定义，如果没有，构建系统将会忽略此目标对象。

### GN complains about a missing `sysroot`.

You likely forgot to run **A** before running **B**.

你可能是忘记在执行步骤 **B** 之前执行步骤 **A**。

> TODO(pylaligand): command showing path to default target


### Internal GN setup

> TODO(pylaligand): .gn, default target, mkbootfs, GN labels insertion

[gn-main]: https://chromium.googlesource.com/chromium/src/tools/gn/+/HEAD/README.md
[gn-preso]: https://docs.google.com/presentation/d/15Zwb53JcncHfEwHpnG_PoIbbzQ3GQi_cpujYwbpcbZo/
[ninja-main]: https://ninja-build.org/
[gn-reference]: https://gn.googlesource.com/gn/+/master/docs/reference.md
[build-project]: https://fuchsia.googlesource.com/build/+/master/
[zircon-getting-started]: https://fuchsia.googlesource.com/zircon/+/master/docs/getting_started.md
[zircon-makefile-options]: https://fuchsia.googlesource.com/zircon/+/master/docs/makefile_options.md

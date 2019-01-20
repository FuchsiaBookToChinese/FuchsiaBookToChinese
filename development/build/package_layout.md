# Layout of Product and Package Directories

# Product 和 Package 目录结构

Each [layer](/development/source_code/layers.md) of the Fuchsia source tree
contains a top-level directory called `packages` containing all the
[build packages](packages.md) for that layer. The present document describes
the packages that are common to all of the layers. Each layer will introduce
additional packages specific to that layer.

Fuchsia 源代码树的每一层（[layer](/development/source_code/layers.md)）包含了一个
名为 `packages` 的顶层目录。该目录包含了该层（layer）所有的构建包（[build packages](packages.md)）。
本文档介绍了所有层都需要使用到的共通的包。各层也会使用一些专用于该层的额外的包。

## Directory map

## 目录地图（目录结构）

In the diagram below, "pkg" refers to Fuchsia packages, the unit of installation
in Fuchsia.

在下面的列表中，“pkg“ 指的是Fuchsia的包，也就是Fuchsia中的安装单元。

```
//<layer>/products
    default          # default build configuration for this layer
                     # by convention, default preinstalls development tools,
                     # and makes all prod packages available.
                     
                     # 该层默认的构建配置
                     # 按照约定，默认情况下会预先安装好开发工具，
                     # 并且保证全部prod包可用。
//<layer>/packages
    <layer>          # all production pkg up to this layer 
                     # 直至此层的全部用于产品化的pkg
    buildbot         # all pkg declared at this layer; used by CQ/CI
                     # 所有在本层声明的pkg；用于CQ/CI
    default          # monolith packages for daily development at this layer
                     # 用于本层日常开发的全部包文件
    preinstall       # devtools for daily development at this layer
                     # 用于本层日常开发的开发工具
    kitchen_sink     # all pkg up to this layer
                     # 直至此层的全部pkg
    all              # grab bag of every pkg in this layer
                     # 此层每个pkg的内容整合
    prod/            # pkg that can be picked up in production
                     # 用于产品化的pkg
    tests/           # correctness tests (target & host)
                     # 正确性测试（目标 & 宿主）
    tools/           # dev tools not for prod (target & host)
                     # 不用于产品化的开发工具（目标 & 宿主）
    benchmarks/      # performance tests
                     # 性能测试
    examples/        # pkg demonstrating features offered by this layer
                     # 用于演示本层特性的pkg
  * experimental/    # pkg not quite ready for prod
                     # 并不完善，尚且不能用于产品化的pkg
  * config/          # config files for the system (e.g. what to boot into)
                     # 系统配置文件（例如启动到什么内容中）
    sdk/             # SDK definitions
                     # SDK相关定义
    ...              # each layer will also define additional packages
                     # 每个层定义的额外的包文件
```

## Cross-layer dependencies

## 跨层交叉依赖（层间依赖）

- `<layer>(N)` depends on `<layer>(N-1)` and adds all the production artifacts
  of (N)
  - this defines a pure production build
- `buildbot(N)` depends on `<layer>(N-1)` and adds all artifacts of (N)
  - this defines a build suitable for verifying the integrity of (N)
- `kitchen_sink(N)` depends on `kitchen_sink(N-1)` and adds all artifacts of (N)
  - this defines a build suitable for developing (N) as well as its dependencies
  
- `<layer>(N)`依赖于`<layer>(N-1)`，以及生产模式下的 (N) 的全部构建产物
  - 其定义的构建是一种纯粹的产品化构建
- `buildbot(N)`依赖于`<layer>(N-1)` ，以及 (N) 的全部构建产物
  - 其定义的构建适用于验证 (N) 的完整性
- `kitchen_sink(N)`依赖于`kitchen_sink(N-1)` ，以及 (N) 的全部构建产物
  - 其定义的构建适用于 (N) 以及 (N) 的依赖内容的开发

## Inner-layer dependencies

## 当前层内部依赖

Most directories in a `packages` directory contain a special `all` package which
aggregates all packages in this directory. Every `all` package should roll up to
the root `all` package, thereby creating a convenient shortcut to build "all
packages in the layer".
Note that the directories that do not require aggregation are marked with `*` in
the diagram above.

`packages` 文件夹下的大多数目录内都包含一个特殊的名为 `all` 的包，用于整合该目录下的所有包。
每个 `all` 包都会上升至顶层的 `all` 包，从而提供了一个快捷的途径以构建该层内所有的包。
需要注意的是，在上面列表中由 `*` 标注的目录不会被整合。

## Disabling packages

## 禁用包

Some packages might need to get (temporarily) disabled as refactorings occur in
the Fuchsia codebase. In order to disable a package `<layer>/<type>/foo`, move
it under `<layer>/<type>/disabled/foo` and remove it from `<layer>/<type>/all`.
Note that this does not apply to packages in directories that do not require
aggregation, as these packages are strictly opt-in already.

在重构Fuchsia代码时，有些包可能需要（临时地）被禁用。例如，如果想要禁用包文件`<layer>/<type>/foo`, 
我们需要把这个包文件移动至 `<layer>/<type>/disabled/foo` , 并且将这个包从 
`<layer>/<type>/all` 内移除。
需要注意的是,这一步操作并不适用于不需要被整合的目录下的包，
因为这些包已经严格遵守了 “可选择加入” 原则。

## Verification

## 验证

The [`//scripts/packages/verify_layer`][verify-layer] tool is used to verify
that a layer's `packages` and `products` directory's structure matches the
description in the present document.

Note that only package files are allowed in such a directory, with the exception
of `README.md` files for documentation purposes.

[`//scripts/packages/verify_layer`][verify-layer] 工具用于验证某层内的 `packages` 和 
`products` 的目录结构是否符合本文档内的描述。

需要注意的是，在这些目录内只允许存放包文件，以及作为文档的 `README.md` 文件。

[verify-layer]: https://fuchsia.googlesource.com/scripts/+/master/packages/README.md


# Products

**Products** are defined in JSON files which can be found at:

**Product**在JSON文件中定义，这些定义文件被存放在如下位置：

* Garnet Layer Products: [`//garnet/products/`][garnet-products-source].
* Peridot Layer Products: [`//peridot/products/`][peridot-products-source].
* Topaz Layer Products: [`//topaz/products/`][topaz-products-source].

* Garnet层Product: [`//garnet/products/`][garnet-products-source].
* Peridot层Product: [`//peridot/products/`][peridot-products-source].
* Topaz层Product: [`//topaz/products/`][topaz-products-source].

Products are a Fuchsia-specific feature built on top of GN to help customize
Fuchsia builds. Products reference [packages](packages.md) and coarsely
define which build artifacts the packages are added to.

“Product”是Fuchsia的一个特性，他们在GN的顶层被构建，可用于Fuchsia的自定义构建。Product
引用了[包](packages.md)并且宽泛地定义了这些包会被构建到哪些构建产物中。

## Package Sets

## 包集合

A product can import one or more packages into three different package sets
of build artifacts, as defined below. The package sets influence what
packages are included in parts of build output.

一个“product”可以将一个包或者多个包导入到如下文定义的构建产物的三个不同的包集合中。
这些包集合影响了哪些包会被包括在构建输出的某些部分之中。

### monolith

The `monolith` section of a **product** defines the list of [build
packages](packages.md) that are to be included in the disk images, system
update images and package repository. Membership of a package in the
`monolith` dependency set takes precedence over membership in other package
sets.

**Product**中的`monolith`部分定义了一个[构建包](packages.md)列表。
这个列表中的构建包会被包含在磁盘镜像，系统更新镜像和包文件仓库源中。
`monolith` 集合中的包优先级高于其他包集合内的包。

### preinstall

The `preinstall` section of a **product** defines the list of [build
packages](packages.md) that are to be preinstalled in the disk image
artifacts of the build, and will also be made available in the package
repository. These packages are not added to the system update images or
packages.

**Product**中的`preinstall`部分定义一个[构建包](packages.md)列表。
这个列表中的构建包会被预先安装在构建产生的磁盘镜像中，并且也可以从包文件仓库源中获取。
这些包不会被加入到系统更新镜像或者系统更新包中。

### available

The `available` section of a **product** defines the list of [build
packages](packages.md) that are added to the package repository only. These
packages will be available for runtime installation, but are not found in
system update images nor are they preinstalled in any disk images. All
members of `monolith` and `preinstall` are inherently `available`.

**Product**中的`available`部分定义一个[构建包](packages.md)列表。
这个列表中的构建包只会被加入到包文件仓库源中。
这些包可以在系统运行时被安装，但他们不会被加入到系统更新镜像，也不会被预先安装到任何磁盘镜像中。
`monolith`和`preinstall`部分中的所有成员本质上也是`available`的（译者：即他们也符合`available`部分的包的标准）。

## Defaults & Conventions

## 默认值 & 惯例

### product: default

The `default` product for a layer, found in `//<layer>/products/default` by
convention contains:

* `monolith` - a common minimal base for this layer that makes up a system
  update.
* `preinstall` - a set of most commonly used development tools for the layer
  and other common work-items.
* `available` - all `prod` packages for the layer.

每个layer的 `default` product被定义在 `//<layer>/products/default` 中，
按照惯例包括以下内容：

* `monolith` - 用于为本层生成系统更新的通常的最小化包集合。
* `preinstall` - 最常用的开发工具集合以及其他常用的工作项。
* `available` - 本层全部的 `prod` 包。

By convention, the `default` product for a higher layer should be additive
from the layer below.

常规来讲，高等级层的 `default` product应当通过其低级层进行构建。

## Inspecting Products

## 审查Products

As products reference [packages](packages.md) and packages may reference
other packages, it is useful to be able to inspect the expanded and filtered
set of build labels that will make up each package set in a product. The
[preprocess products][preprocess-products-py] script is the tool that
produces this for the build and can be run by hand:

因为products引用了[包](packages.md)，并且这些包可能又引用了其他包，
所以对将被用于生成product中每个包集合的构建标签的扩展集和过滤集进行审查是非常有意义的。
[preprocess products][preprocess-products-py]脚本为构建操作提供了审查功能，
同时该脚本也可以被手动执行：

```bash
$ python build/gn/preprocess_products.py --products '["garnet/products/default"]'
```

[garnet-products-source]: https://fuchsia.googlesource.com/garnet/+/master/products/
[peridot-products-source]: https://fuchsia.googlesource.com/peridot/+/master/products/
[topaz-products-source]: https://fuchsia.googlesource.com/topaz/+/master/products/
[preprocess-products-py]: https://fuchsia.googlesource.com/build/+/master/gn/preprocess_products.py

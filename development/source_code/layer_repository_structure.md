# Layer repository structure
# 项目目录结构

The garnet, peridot, and topaz layers share some structure.
This section documents these common aspects.

石榴石，橄榄石和黄玉层共享一些结构。本节介绍这些共同的部分。


## public/
## public/

The `public/` directory defines the public interface to this repository. Code
outside this repository should not depend on files outside of this directory.
This directory should not depend on any files in this repository outside of this
directory. This property ensures that code outside this repository does not
transitively depend on any of the private files in this repository.

`public /` 目录定义了这个项目的公共接口。 此项目之外的代码不应该依赖于此目录之外的文件。
这个目录也不应该依赖这个项目中除此目录以外的其他任何目录的文件。该属性确保此项目外的代码
不会可依赖于此项目中的任何私有文件。

### public/build/
### public/build/

The `public/build/` directory contains files that support the build systems of
clients of the `public/` directory.

`public / build /` 目录包含支持构建基于 `public /` 目录的客户端系统的文件。

### public/lib/
### public/lib/

The `public/lib/` directory contains libraries (both static and dynamic) that
clients can link into their processes. Many of the libraries in this directory
are defined in FIDL, which are language-agnostic definitions of interprocess
communication protocols. However, this directory also contains manually
implemented libraries in various languages. In these cases, both the headers and
source files for these libraries are included in this directory.

`public / lib /` 目录包含一些库（包括静态和动态），客户端可以用来链接到他们的进程中。 这个目录中的许多库
在FIDL中定义，FIDL即进程间通信协议。该目录也包含用各种语言实现的库。 在这些情况下，头文件和这些库的源文件
也包含在这个目录中。


Libraries that are private implementation details of this repository (i.e., not
part of this repository's public interface) should be in `lib/` instead.

该项目的私有实现的库（即，不是这个库的公共接口的一部分）应该放在 `lib /` 文件夹中。

## bin/
## bin/

The `bin/` directory contains executable binaries. Typically, these binaries
implement one or more of the interfaces defined in `public/`, but the binaries
themselves are not part of the public interface of this repository.

`bin /` 目录包含可执行的二进制文件。 通常，这些二进制文件实现 `public /` 中定义的一个或多个接口，但是这些二进制文件
他们本身不是这个项目的公共接口的一部分。

## docs/
## docs/

The `docs/` directory contains documentation about this repository.

`docs /` 目录包含有关此项目的文档。

## examples/
## examples/

The `examples/` directory contains code that demonstrates how to use the public
interfaces exposed by this repository. This code should not be required for the
system to execute correctly and should not depend on the private code in this
repository.


`examples /` 目录包含一些代码，演示如何使用此项目的公开接口。 系统正确执行时不应该需要此代码，并且不应依赖此项目
中的专用代码。

## infra/
## infra/

If the repository needs infrastructure related files, e.g., CQ configs,
then these files go here.

如果项目需要与基础设施相关的文件，例如CQ配置，那么这些文件就在这里。

## lib/
## lib/

The `lib/` directory contains libraries (both static and dynamic) that are used
internally by this repository. These libraries are internal implementation
details of this repository and should not be used by code outside this
repository.


`lib /` 目录包含项目内部使用的库（包括静态和动态）。 这些库是项目的内部实现，不应该被项目之外的代码使用。

Libraries that are part of the public interface of this repository (i.e., not
private implementation details) should be in `public/lib/` instead.

作为项目的公共接口的一部分的库（即，不是私有实现）应该放到在 `public / lib /` 文件夹中。

## manifest/
## manifest/

The `manifest/` directory contains jiri manifests for repo.

`manifest /`目录包含项目所需的jiri manifests

## packages/
## packages/

The `packages/` directory contains package definitions for each
package in the repo.

`packages /` 目录包含了项目的各个包

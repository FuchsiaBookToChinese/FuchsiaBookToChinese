# Glossary
# 术语表


## Overview
## 概览

The purpose of this document is to provide short definitions for a collection
of technical terms used in the Fuchsia source tree.
本文档的目的是为 Fuchsia 源代码树中使用的技术术语集作出简短的定义。

#### Adding new definitions
#### 添加新的定义

- A definition should be limited to two or three sentences and deliver a
high-level description of a term.
- 一个定义应该限制在两到三个句子，并且提供术语的高级描述。
- When another non-trivial technical term needs to be employed as part of the
description, consider adding a definition for that term and linking to it from
the original definition.
- 当描述的一部分需要引用另一个非直观的技术术语时，可以考虑为该术语添加定义并从原使定义链接到该术语。
- A definition should be complemented by a list of links to more detailed
documentation and related topics.
- 定义应该由更详细的文档和相关主题的链接列表来补充


## Terms
## 术语

#### **Agent**

A component whose lifecycle is not tied to any story, is a singleton in user
scope, and provides services to other components. An agent can be invoked by
other components or by the system in response to triggers like push
notifications. An agent can provide services to components, send and receive
messages, and make proposals to give suggestions to the user.
一个 component 的生命周期不被任何 story 管理的，是用户范围里的一个单独例子，并提供服务给其他 component。
一个 Agent 可以被其他 component 或系统在响应诸如推送通知等触发器时所调用。
一个 Agent 可以提供服务给 component，例如发送和接收消息，提供方案来给予用户建议。

#### **AppMgr**

The Application Manager (AppMgr) is responsible for launching applications and
managing the namespaces in which those applications run. It is the first process
started in the `fuchsia` job by the [DevMgr](#DevMgr).
应用程序管理器(AppMgr)负责启动应用程序和管理这些应用程序运行时所在的命名空间。
这是第一个在 `fuchsia` job 裡被 [DevMgr](#DevMgr) 启动的进程。

#### **Armadillo**

An implementation of a user shell.
一个 user shell 的实现。

- [Source](https://fuchsia.googlesource.com/topaz/+/master/shell/armadillo_user_shell/)
- [资源](https://fuchsia.googlesource.com/topaz/+/master/shell/armadillo_user_shell/)

#### **Base shell**

The platform-guaranteed set of software functionality which provides a basic
user-facing interface for boot, first-use, authentication, escape from and
selection of user shells, and device recovery.
平台保证的软件功能集，为引导、首次使用、身份验证、用户外壳的退出和选择以及设备恢复提供了基本的面向用户的接口。

#### **Component**

Analogous to a software package, a component encapsulates a single installable
resource. It may contain data, code, assets or a combination thereof. Components
contain a JSON manifest that describes a number of facets. Components include,
but are certainly not limited to modules and agents.
与软件包类似，component 封装了单个可安装的资源。它可能包含数据、代码、资产或其组合。Components 包含一个用于概况介绍的 JSON 清单。
Components 包括但不限于 modules 和 agents。

#### **Channel**

A Channel is the fundamental IPC primitive provided by Zircon.  It is a bidirectional,
datagram-like transport that can transfer small messages including
[Handles](#Handle).
- [Channel Overview](https://fuchsia.googlesource.com/zircon/+/master/docs/objects/channel.md)
一个 Channel 是 Zircon 提供的 IPC 基元。 它是一种双向的，类似数据报的，可以传输包括 [Handles](#Handle) 在内的小消息的传输方式。
- [Channel 概览](https://fuchsia.googlesource.com/zircon/+/master/docs/objects/channel.md)

#### **DevHost**

A Device Host (DevHost) is a process containing one or more device drivers.  They are
created by the Device Manager, as needed, to provide isolation between drivers for
stability and security.
设备主机(DevHost)是一个包含一个或多个设备驱动程序的进程。它们由设备管理器根据稳定性和安全性的需求而创建，主要用于提供驱动程序之间的隔离带。

#### **DevMgr**

The Device Manager (DevMgr) is responsible for enumerating, loading, and managing the
lifecycle of device drivers, as well as low level system tasks (providing filesystem
servers for the boot filesystem, launching [AppMgr](#AppMgr), and so on).
设备管理器(DevMgr)负责枚举，加载和管理设备驱动程序的生命周期，以及低级系统任务(为启动文件系统提供文件系统服务器，启动 [AppMgr](#AppMgr) 等)。

#### **DDK**

The Driver Development Kit is the documentation, APIs, and ABIs necessary to build Zircon
Device Drivers.  Device drivers are implemented as ELF shared libraries loaded by Zircon's
Device Manager.
- [DDK includes](https://fuchsia.googlesource.com/zircon/+/master/system/ulib/ddk/include/ddk/)
驱动程序开发工具包包含了构建 Zircon 设备驱动程序必要的文档、API 和 ABIs。设备驱动程序是由 Zircon 的设备管理器加载的 ELF 共享库实现的。
- [DDK 包括](https://fuchsia.googlesource.com/zircon/+/master/system/ulib/ddk/include/ddk/)

#### **Escher**

Graphics library for compositing user interface content. Its design is inspired
by modern real-time and physically based rendering techniques though we
anticipate most of the content it renders to have non-realistic or stylized
qualities suitable for user interfaces.
用于合成用户界面内容的图形库。它的设计是受现代实时和物理渲染技术的启发，尽管我们预期它呈现的大部分内容是适用于用户界面的非真实的或程式化的质地。

#### **FAR**

The Fuchsia Archive Format is a container for files to be used by Zircon and Fuchsia.
It will replace Zircon's older BootFS container and be used in Fuchsia Packages.
- [FAR Spec](the-book/archive_format.md)
Fuchsia 档案格式是一个被 Zircon 和 Fuchsia 使用的文件容器。 它将取代 Zircon 中较旧的 BootFS 容器并用于 Fuchsia Packages。
- [FAR 规格](the-book/archive_format.md)

#### **fdio**

fdio is the Zircon IO Library.  It provides the implementation of posix-style open(), close(),
read(), write(), select(), poll(), etc, against the RemoteIO RPC protocol.  These APIs are
return-not-supported stubs in libc, and linking against libfdio overrides these stubs with
functional implementations.
- [Source](https://fuchsia.googlesource.com/zircon/+/master/system/ulib/fdio/)
fdio 是 Zircon 的 IO 库。 它针对 RemoteIO RPC 协议，提供了 posix 样式的 open()，close()，read()，write()，select()，poll()等
的实现。 这些 API 是 libc 中不支持返回的存根，并且链接到 libfdio 会覆盖这些存根与功能的实现。
- [资源](https://fuchsia.googlesource.com/zircon/+/master/system/ulib/fdio/)

#### **FIDL**

The Fuchsia Interface Definition Language (FIDL) is a language for defining protocols
for use over [Channels](#channel). FIDL is programming language agnostic and has
bindings for many popular languages, including C, C++, Dart, Go, and Rust. This
approach lets system components written in a variety of languages interact seamlessly.
Fuchsia 接口定义语言(FIDL)是一种用于定义 [Channels](#channel) 上使用的协议的语言。
FIDL 与编程语言无关，并且对许多流行语言都提供了支持，包括C、C++、Dart、Go 和 Rust。
这种方式让使用多种语言编写的系统 component 得以无缝地交互。

#### **Flutter**

[Flutter](https://flutter.io/) is a functional-reactive user interface framework
optimized for Fuchsia and is used by many system components. Flutter also runs on
a variety of other platform, including Android and iOS. Fuchsia itself does not
require you to use any particular language or user interface framework.
[Flutter](https://flutter.io/)是针对 Fuchsia 优化的功能反应性用户界面框架，并被许多系统 component 使用。 Flutter 也能在各种其它平台上运行，包括 Android 和 iOS。 
Fuchsia本身并不要求你使用任何特定的语言或用户界面框架。

#### **FVM**

Fuchsia Volume Manager is a partition manager providing dynamically allocated
groups of blocks known as slices into a virtual block address space. The
FVM partitions provide a block interface enabling filesystems to interact
with it in a manner largely consistent with a regular block device.
- [Filesystems](the-book/filesystems.md)
fuchsia 卷管理器是一个分区管理器，能动态分配块集合（也成为切片）到虚拟块地址空间中。FVM 分区提供了一个块接口，使文件系统能够以与常规块设备基本一致的方式与其进行交互。
- [Filesystems](the-book/filesystems.md)

#### **Garnet**

Garnet is one of the four layers of the Fuchsia codebase.
- [The Fuchsia layercake](layers.md)
- [Source](https://fuchsia.googlesource.com/garnet/+/master)
  
Garnet 是 Fuchsia 代码库中四个层(layer)之一。
- [Fuchsia 千层蛋糕](layers.md)
- [资源](https://fuchsia.googlesource.com/garnet/+/master)


#### **GN**

GN is a meta-build system which generates build files so that Fuchsia can be
built with [Ninja](#ninja).
GN is fast and comes with solid tools to manage and explore dependencies.
GN files, named `BUILD.gn`, are located all over the repository.
- [Language and operation](https://chromium.googlesource.com/chromium/src/+/master/tools/gn/docs/language.md)
- [Reference](https://chromium.googlesource.com/chromium/src/tools/gn/+/HEAD/docs/reference.md)
- [Fuchsia build overview](build_overview.md)
GN 是一个元构建系统，它生成构建文件，以便可以使用 [Ninja](#ninja) 构建 Fuchsia。
GN 速度快，并配有可靠的工具来管理和探索依赖项。
文件名为 `BUILD.gn` 的 GN 文件位于整个仓库中。
- [语言和操作](https://chromium.googlesource.com/chromium/src/+/master/tools/gn/docs/language.md)
- [参考](https://chromium.googlesource.com/chromium/src/tools/gn/+/HEAD/docs/reference.md)
- [Fuchsia 构建概览](build_overview.md)

#### **Handle**

The "file descriptor" of the Zircon kernel.  A Handle is how a userspace process refers
to a kernel object.  They can be passed to other processes over [Channel](#Channel)s.
- [Handle (in Zircon Concepts Doc)](https://fuchsia.googlesource.com/zircon/+/master/docs/concepts.md)

Zircon 内核的“文件描述符”。Handle 是用户空间进程引用内核对象的方式。他们可以通过 [Channel](#Channel) 传递给其它进程。
- [Handle (Zircon 概念文档內)](https://fuchsia.googlesource.com/zircon/+/master/docs/concepts.md)

#### **Jiri**

Jiri is a tool for multi-repo development. It is used to checkout the Fuchsia
codebase. It supports various subcommands which makes it easy for developers to
manage their local checkouts.
- [Reference](https://fuchsia.googlesource.com/jiri/+/master/README.md)
- [Sub commands](https://fuchsia.googlesource.com/jiri/+/master/README.md#main-commands-are)
- [Behaviour](https://fuchsia.googlesource.com/jiri/+/master/behaviour.md)
- [Tips and tricks](https://fuchsia.googlesource.com/jiri/+/master/howdoi.md)

Jiri 是一个用于多仓库开发的工具。 它用于检出 Fuchsia 代码库。 它支持各种子命令，这使得开发人员可以更方便地管理他们本地的检出。
- [参考](https://fuchsia.googlesource.com/jiri/+/master/README.md)
- [子命令](https://fuchsia.googlesource.com/jiri/+/master/README.md#main-commands-are)
- [行为](https://fuchsia.googlesource.com/jiri/+/master/behaviour.md)
- [提示和技巧](https://fuchsia.googlesource.com/jiri/+/master/howdoi.md)

#### **Job**

TODO(cpu): add definition

#### **Launchpad**

[Launchpad](the-book/launchpad.md) is a library provided by Zircon that provides the
functionality to create and start new processes (including loading ELF binaries,
passing initial RPC messages needed by runtime init, etc).  It is a low-level
library and over time it is expected that few pieces of code will make direct
use of it.
- [Launchpad API (launchpad.h)](https://fuchsia.googlesource.com/zircon/+/master/system/ulib/launchpad/include/launchpad/launchpad.h)
  
[Launchpad](the-book/launchpad.md)是 Zircon 提供的一个库，提供了创建和启动新进程的功能(包括加载 ELF 二进制文件，传递运行时初始化所需的初始 RPC 消息，等等)。 这是一个低级库，随着时间的推移，预计只有几段代码会直接使用它。
- [Launchpad API (launchpad.h)](https://fuchsia.googlesource.com/zircon/+/master/system/ulib/launchpad/include/launchpad/launchpad.h)

#### **Ledger**

[Ledger](https://fuchsia.googlesource.com/peridot/+/master/docs/ledger/README.md)
is a distributed storage system for Fuchsia. Applications use Ledger either
directly or through state synchronization primitives exposed by the Modular
framework that are based on Ledger under-the-hood.

[Ledger](https://fuchsia.googlesource.com/peridot/+/master/docs/ledger/README.md) 是用于 Fuchsia 的分布式存储系统。应用程序直接使用 Ledger, 或通过基于底层 Ledger 的状态同步原函数，这些状态同步原函数以模块化框架的形式暴露。

#### **LK**

Little Kernel (LK) is the embedded kernel that formed the core of the Zircon Kernel.
LK is more microcontroller-centric and lacks support for MMUs, userspace, system calls --
features that Zircon added.
- [LK on Github](https://github.com/littlekernel/lk)

Little Kernel(LK) 是构成 Zircon Kernel 内核的嵌入式内核。 LK 更多的是以微控制器为中心的，它缺少对MMU、用户空间和系统调用的支持——这些功能往往由 Zircon 添加。
- [LK在Github](https://github.com/littlekernel/lk)

#### **Maxwell**

Services to expose ambient and task-related context, suggestions and
infrastructure for leveraging machine intelligence.

用于暴露环境和任务相关内容的服务，提供建议和基础组件以利用机器智能。

#### **Module**

A component with a `module` metadata file which primarily describes the
Module's data compatibility and semantic role.

Modules show UI and participate in Stories at runtime.

一个具有 `module` 元数据文件的 component，主要描述 Module 的数据兼容性和语义角色。

Moudels 显示 UI，并在运行时参与 Stories。

- [module metadata format](https://fuchsia.googlesource.com/peridot/+/HEAD/docs/modular/manifests/module.md)

- [module 元数据格式 ](https://fuchsia.googlesource.com/peridot/+/HEAD/docs/modular/manifests/module.md)

#### **Mozart**

The view subsystem. Includes views, input, compositor, and GPU service.
视图子系统。包含了视图、输入、合成以及 GPU 服务。

#### **Musl**

Fuchsia's standard C library (libc) is based on Musl Libc.
- [Source](https://fuchsia.googlesource.com/zircon/+/master/third_party/ulib/musl/)
- [Musl Homepage](https://www.musl-libc.org/)

Fuchsia 的标准 C 库基于 Musl Libc
- [资源](https://fuchsia.googlesource.com/zircon/+/master/third_party/ulib/musl/)
- [Musl 主页](https://www.musl-libc.org/)

#### **Namespace**

A namespace is the composite hierarchy of files, directories, sockets, [service](#Service)s,
and other named objects which are offered to application components by their
[environment](#Environment).
- [Fuchsia Namespace Spec](the-book/namespaces.md)

一个 Namespace 是一种特定的复合层次结构，主要针对于文件、目录、套接字、[service](#Service)，以及其它由各自[environment](#Environment) 提供了引用程序组件的命名对象。
[environment](#Environment).
- [Fuchsia Namespace Spec](the-book/namespaces.md)

#### **Netstack**

TODO(tkilbourn): add definition.

#### **Ninja**

Ninja is the build system executing Fuchsia builds.
It is a small build system with a strong emphasis on speed.
Unlike other systems, Ninja files are not supposed to be manually written but
should be generated by more featureful systems, such as [GN](#gn) in Fuchsia.
- [Manual](https://ninja-build.org/manual.html)
- [Ninja rules in GN](https://chromium.googlesource.com/chromium/src/tools/gn/+/HEAD/docs/reference.md#ninja_rules)
- [Fuchsia build overview](build_overview.md)

Ninja 是用于执行 fuchsia 构建的构建系统。
它是一个极其强调速度的小型构建系统。
不同于其它系统，Ninja 文件不应当手动编写，应当由更具特性化的系统生产，例如 Fuchsia 中的 [GN](#gn)。
- [Manual](https://ninja-build.org/manual.html)
- [Ninja rules in GN](https://chromium.googlesource.com/chromium/src/tools/gn/+/HEAD/docs/reference.md#ninja_rules)
- [Fuchsia build overview](build_overview.md)
  
#### **Paver**

A tool in Zircon that installs partition images to internal storage of a device.
- [Guide for installing Fuchsia with paver](fuchsia_paver.md).

在 zircon 中，一个用于将分区映像安装到设备内部存储的工具。
- [Guide for installing Fuchsia with paver](fuchsia_paver.md).

#### **Peridot**

Peridot is one of the four layers of the Fuchsia codebase.
- [The Fuchsia layercake](layers.md)
- [Source](https://fuchsia.googlesource.com/peridot/+/master)

Peridot 是 Fuchsia 代码库中四个层(layer)之一。
- [The Fuchsia 千层蛋糕](layers.md)
- [资源](https://fuchsia.googlesource.com/peridot/+/master)

#### **RemoteIO**

RemoteIO is the Zircon RPC protocol used between fdio (open/close/read/write/ioctl)
and filesystems, device drivers, etc.  As part of [FIDL](#FIDL) v2, it will become a set
of FIDL Interfaces (File, Directory, Device, ...) allowing easier interoperability,
and more flexible asynchronous IO for clients or servers.

RemoteIO 是一个 Zircon PRC 协议，被用于 fdio (open/close/read/write/ioctl)，文件系统以及设备驱动，等等。作为 [FIDL](#FIDL) v2 的一部分，它将成为一组 FIDL 接口(文件，目录，设备……)，拥有友好的互操作性，以及更灵活的客户端和服务器异步 IO。

#### **Service**

A service is an implementation of a [FIDL](#FIDL) interface. Components can offer
their creator a set of services, which the creator can either use directly or
offer to other components.

Services can also be obtained by interface name from a [Namespace](#namespace),
which lets the component that created the namespace pick the implementation of
the interface. Long-running services, such as [Mozart](#mozart), are typically
obtained through a [Namespace](#namespace), which lets many clients connect to a
common implementation.

一个 service 是一个 [FIDL] 接口的实现。其 Components 可以为它们的创建者提供一系列的 services，创建者可以直接使用这些 services，或者用于其它的 Components。

Services 也可以由一个 [Namespace](#namespace) 的接口名称获得，这使得创建了 namespace 的 component 可以选择接口的实现。长期运行的服务，例如 [Mozart](#mozart)，通常由一个可以让多个客户端连接到一个通用的实现的 [Namespace](#namespace) 获得。

#### **Story**

A user-facing logical container encapsulating human activity, satisfied by one
or more related modules. Stories allow users to organize activities in ways they
find natural, without developers having to imagine all those ways ahead of time.

一个面向用户的用于封装人类活动的逻辑容器，由一个和或者多个相联系的 modules 所满足。
Stories 允许用户以他们自在的方式组织活动，而开发人员不必提前想象所有的这些方式

#### **Story Shell**

The system responsible for the visual presentation of a story. Includes the
presenter component, plus structure and state information read from each story.

系统对一个 story 的可视化介绍的响应。包括用于演示的 component、升级的结构以及读取自每个 story 的状态信息。

#### **Topaz**

Topaz is one of the four layers of the Fuchsia codebase.
- [The Fuchsia layercake](layers.md)
- [Source](https://fuchsia.googlesource.com/topaz/+/master)

Topaz 是 Fuchsia 代码库中四个层(layer)之一。
- [The Fuchsia 千层蛋糕](layers.md)
- [资源](https://fuchsia.googlesource.com/topaz/+/master)

#### **User Shell**

The user-specific and replaceable set of software functionality that works in
conjunction with devices to create an environment in which people can interact
with modules.

用户专用且可替换的一组软件功能，与设备结合使用，以创建人与 modules 可以交互的 environment

#### **VDSO**

The VDSO is a Virtual Shared Library -- it is provided by the [Zircon](#Zircon) kernel
and does not appear in the filesystem or a package.  It provides the Zircon System Call
API/ABI to userspace processes in the form of an ELF library that's "always there."
In the Fuchsia SDK and [Zircon DDK](#DDK) it exists as `libzircon.so` for the purpose of
having something to pass to the linker representing the VDSO.

VDSO 是一个虚拟共享库——它由 [Zircon](#Zircon) 核心提供，并且不会在文件系统或者包内显示。它以一个
“永远存在”的 ELF 库的形式向用户空间进程提供 Zircon 系统调用 API/ABI。
在 Fuchsia SDK 以及 [Zircon DDK](#DDK) 中，它以一个 `libzircon.so` 文件的形式存在，目的是将某些内容传递给代表 VDSO 的链接器。


#### **VMAR**

A Virtual Memory Address Range is a Zircon Kernel Object that controls where and how
VMOs may be mapped into the address space of a process.
- [VMAR Overview](https://fuchsia.googlesource.com/zircon/+/master/docs/objects/vm_address_region.md)

虚拟内存地址范围是一个 Zircon 内核对象，它控制 VMO 在何处以及如何映射到进程的地址空间。
- [VMAR 概览](https://fuchsia.googlesource.com/zircon/+/master/docs/objects/vm_address_region.md)
  
#### **VMO**

A Virtual Memory Object is a Zircon Kernel Object that represents a collection of pages
(or the potential for pages) which may be read, written, mapped into the address space of
a process, or shared with another process by passing a Handle over a Channel.
- [VMO Overview](https://fuchsia.googlesource.com/zircon/+/master/docs/objects/vm_object.md)

虚拟内存对象是一个 zircon 内核对象，它代表一个可以读取、写入、映射至进程的地址空间或能通过 Channel 传递 Handle 共享至其它进程的页面集合(或是潜在页面的集合)。

#### **Zedboot** ####

TOO(raggi): add definition.s

#### **Zircon**

Zircon is the [microkernel](https://en.wikipedia.org/wiki/Microkernel) and lowest level
userspace components (driver runtime environment, core drivers, libc, etc) at the core of
Fuchsia.  In a traditional monolithic kernel, many of the userspace components of Zircon
would be part of the kernel itself.
Zircon is also one of the four layers of the Fuchsia codebase.
- [Zircon Documentation](https://fuchsia.googlesource.com/zircon/+/master/README.md)
- [Zircon Concepts](https://fuchsia.googlesource.com/zircon/+/master/docs/concepts.md)
- [The Fuchsia layercake](layers.md)
- [Source](https://fuchsia.googlesource.com/zircon/+/master)

Zircon 是 Fuchsia 核心的 [微内核](https://en.wikipedia.org/wiki/Microkernel)和最低等级的用户
空间 components(驱动程序运行时的环境，核心驱动程序，libc 等)。在传统的单片内核中，许多 Zircon 
用户空间 component 将成为内核自己的一部分。
Zircon 同时也是 Fuchsia 代码库中四个层(layer)之一。
- [Zircon 文档](https://fuchsia.googlesource.com/zircon/+/master/README.md)
- [Zircon 概念](https://fuchsia.googlesource.com/zircon/+/master/docs/concepts.md)
- [The Fuchsia 千层蛋糕](layers.md)
- [资源](https://fuchsia.googlesource.com/zircon/+/master)

#### **ZX**

ZX is an abbreviation of "Zircon" used in Zircon C APIs/ABIs (`zx_channel_create()`, `zx_handle_t`,
 `ZX_EVENT_SIGNALED`, etc) and libraries (libzx in particular).

ZX 是在 Zircon C APIs/ABIs(`zx_channel_create()`, `zx_handle_t`,
 `ZX_EVENT_SIGNALED` 等) 和库中使用的 “Zircon” 的缩写。

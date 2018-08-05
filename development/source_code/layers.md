# The Fuchsia layer cake
# Fuchsia 千层糕

Fuchsia is the name of the open source project and the complete technical
artifact produced by the open source project. The name "Fuchsia" appears in many
places throughout the codebase, and will be baked into API names exposed to
third-party developers. The names of the individual layers below (with the
exception of Zircon) are implementation details of how we develop Fuchsia, and
we should avoid baking those names into public APIs.

Fuchsia 是开源项目的名称和由开源项目制作的完整的技术产品。“Fuchsia”在代码库中的很多地方出现，并且将会加入 API 中暴露给第三方开发者。下面每一个层的命名（除了 Zircon 之外）都是我们开发 Fuchsia 的具体实现，我们应该避免将这些命名加入到公共API里。

## Zircon
## Zircon


Zircon is the operating system's foundation: it mediates hardware access,
implements essential software abstractions over shared resources, and provides a
platform for low-level software development.

For example, Zircon contains the kernel, device manager, most core and
first-party device drivers, and low-level system libraries, such as libc and
launchpad. Zircon also defines the Fuchsia IDL (FIDL), which is the protocol
spoken between processes in the system, as well as backends for C and C++. The
backends for other languages will be added by other layers.

Zircon 是操作系统的基础：它调解硬件访问，在共享资源上实现基本的软件抽象，并提供一个低级的软件开发平台。

例如，Zircon 包含了内核，设备管理器，最核心的第一方设备驱动和底层系统库，比如 libc 和 launchpad 。Zircon 还定义了 Fuchsia IDL (FIDL)，这是系统中进程间交流的协议，也是 C 和 C++ 的后端。其他语言的后端会加到其他层中。

## Garnet
## Garnet


Garnet provides device-level system services for software installation,
administration, communication with remote systems, and product deployment.

For example, Garnet contains the network, media, and graphics services. Garnet
also contains the package management and update system.

Garnet 为软件安装、管理、远程系统通讯以及产品部署提供了设备层的系统服务。
例如，Garnet 包含了网络、媒体和图像服务，它也包含了包管理以及更新系统服务。

## Peridot
## Peridot


Peridot provides the services needed to create a cohesive, customizable,
multi-device user experience assembled from modules, stories, agents, entities,
and other components.

For example, Peridot contains the device, user, and story runners. Peridot also
contains the ledger and resolver, as well as the context and suggestion engines.

Peridot 提供的服务需要创建一个内聚的、可定制的，通过模块、story、代理、实体和其他组件组装的多设备用户体验。

例如，Peridot 包含了设备，用户和 story runners，也包含了 ledger 和 解析器，以及上下文和建议引擎。

## Topaz
## Topaz


Topaz augments system functionality by implementing interfaces defined by
underlying layers. Topaz contains four major categories of software: modules,
agents, shells, and runners.

For example, modules include the calendar, email, and terminal modules, shells
include the base shell and the user shell, agents include the email and chat
content providers, and runners include the Web, Dart, and Flutter runners.

Topaz 通过实现底层定义的接口来增强系统功能，它包含了四大类软件：模块、代理、shell 和 runner.
例如，模块包括日历，电子邮件，以及终端模块，shells 包括基础 shell 和用户 shell ,代理包括了电子邮件和 chat content_provider，而运行时则包括了 web_runner，dart_runner和flutter_runner.
# Fuchsia

Pink + Purple == Fuchsia (一个全新的操作系统)

欢迎来到 Fuchsia 的世界! 通过这个文档，你可以快速开始了解 Fuchsia。

*** 
注意：Fuchsia Source 包含 Fuchsia 的内核平台
[Zircon](https://fuchsia.googlesource.com/zircon/+/master/README.md)。
在构建 Fuchsia 的过程中，Zircon 也会同时被构建；如果你想单独使用 Zircon，请参阅 [Getting Started](https://fuchsia.googlesource.com/zircon/+/master/docs/getting_started.md) 文档并跟随文档中的步骤操作。
***

## 前提条件

### 准备好你的构建环境

### Ubuntu 系统

```
sudo apt-get install texinfo libglib2.0-dev liblz4-tool autoconf libtool libsdl-dev build-essential golang git build-essential curl unzip
```

### macOS

安装 Xcode 命令行工具
```
xcode-select --install
```

安装最新版本的 Xcode

安装其他的依赖：

* 使用 Homebrew

```
# 安装 Homebrew
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)
# 安装包
brew install wget pkg-config glib autoconf automake libtool golang
```

* 使用 MacPorts
```
# 安装 MacPorts
# 详情见 https://guide.macports.org/chunked/installing.macports.html
port install auto conf auto make lib tool libpixman pkgconfig glib2
```


### [Googler 通道] Goma

要使用快速构建，请确保你的设备上安装了 goma。

## 构建 Fuchsia

### 获取源代码

按照[获取 Fuchsia 源代码的说明](getting_source.md)操作后，再回到此文档继续如下步骤。

### 构建

如果你已将源代码中的 `.jiri_root/bin` 目录添加到了你的环境变量中，那么 `fx` 命令也应该同样存在于环境变量中了。 如果没有，那么使用 `scripts / fx` 来执行命令也是可行的。

```
fx set x86-64
fx full-build
```

第一行命令设置了你想要构建的的构建参数，并将生成的构建系统导出至目录(例如 `out/debug-x86-64`)

第二行命令开始执行构建，将源代码转化为构建文件。如果你修改了源代码的结构，则可以通过单独运行 `fx full-build` 命令执行增量构建。

或者，您可以按照[直接构建底层系统](build_system.md)文档进行构建。

#### [可选]自定义构建环境

默认情况下，您将得到一个 x86-64 架构的 debug 版本。 除非你一些额外的构建需求，否则的话，你可以跳过这一章节。

通过运行 `fset-usage` ，你可以查看构建的选项列表。 以下是一些例子：

```
fx set x86-64              # x86-64 debug build
fx set arm64               # arm64 debug build
fx set x86-64 --release    # x86-64 release build
```

### [可选]使用`ccache`和`goma`加速构建

`ccache` 通过缓存先前构建过的的组建来加速构建。如果你已经正确设置了 `CCACHE_DIR` 环境变量，那么 `ccache` 将会自动启用。

[Googlers 专属：`goma` 通过在多台设备上进行分布式构建来加速。
如果你已在在 `~/goma` 中安装了 `goma`，那么它也将默认启用，并优先于 `ccache`。]

要覆盖默认行为，可为`fx set` 指令附加以下的参数：

```
--ccache     # force use of ccache even if goma is available
--no-ccache  # disable use of ccache
--no-goma    # disable use of goma
```

## 启动 Fuchsia

### 在硬件中安装和启动

要在硬件上运行 Fuchsia ，你需要使用 paver，阅读这份
[说明](fuchsia_paver.md)能帮助你启动和运行 Fuchsia。

### 从QEMU启动

如果你没有受支持的硬件，那么你可以在[QEMU](https://fuchsia.googlesource.com/zircon/+/HEAD/docs/qemu.md) 模拟器中运行 Fuchsia。
Fuchsia 的 `buildtools/qemu` 目录中，已经包括了为 QEMU 预构建的二进制文件。

`fx run` 命令将根据本地生成的 `user.bootfs` 文件在 QEMU 中启动 Zircon

```
fx run
```

以下是 `fx run` 命令可附加的参数，用来对 QEMU 进行控制：

* `-m` 设置 QEMU 的内存单位为 MB。
* `-g` 启用图形界面(见下文)。
* `-N` 启用网络连接(见下文)。

使用 `fx run -h` 命令查看所有可用的选项。

#### 启用图形界面

注意：由于缺乏 Vulkan 的支持，QEMU 下的图形界面的效果将大打折扣。只有 Zircon 内核的 UI 界面得以呈现。

要在 QEMU 下启用图形界面，请将 `-g` 参数添加到 `fx run`：

```
fx run -g
```

#### 启用网络

注意：QEMU 中的联网功能仅能在 x86_64 架构下使用。

首先，[配置](https://fuchsia.googlesource.com/zircon/+/master/docs/qemu.md#Enabling-Netwoking-under-QEMU-x86_64-only)一个 QEMU 使用的虚拟接口。

完成后，您可以将 `-N` 和 `-u` 参数附加到 `fx run`：

```
fx run -N -u $FUCHSIA_SCRIPTS_DIR/start-dhcp-server.sh
```

在`-u`标志中运行一个脚本来建立一个本地的 DHCP 服务和 NAT 来配置
 IPv4 接口和路由。

## 探索Fuchsia

当 Fuchsia 启动并显示 "$" shell提示符时，你就可以运行程序了！

例如，要获得更多内容，请运行：

```
fortune
```

### 选择一个标签

Fuchsia 在启动后将会显示显示多个标签。 当前选择的标签在
屏幕顶部以黄色突出显示。 你可以使用键盘上的 Alt-Tab 键切换到下一个标签。

- 标签 0 是控制台，并且显示启动选项和应用程序日志。
- 标签 1 , 2 和 3 包含 shells。
- 标签 4 和更高版本包含您已启动的应用程序。

注意：要选择标签，您可能需要输入 "console mode"。 有关详细信息，请参阅下一节。

### 启动一个图形化的应用

QEMU 不支持 Vulkan，因此无法运行我们的图形堆栈。

大多数在 Fuchsia 的图形应用程序使用
[Mozart](https://fuchsia.googlesource.com/garnet/+/master/bin/ui/) 系统合成器。通常你可以在在 `/system/apps` 中找到可以这样启动的程序，例如：

```
launch spinning_square_view
```

Mozart 示例应用程序的源代码在
[这里](https://fuchsia.googlesource.com/garnet/+/master/examples/ui)。

当你打开了使用 Mozart 的程序，使用硬件图形加速，或者通过构建
[default](https://fuchsia.googlesource.com/topaz/+/master/packages/default) 包(这可以启动时进入 Fuchsia 的系统 UI 界面)时，Fuchsia 将进入 "graphics mode"，这将不会显示任何的文本 shell。如果要使用文本不 shell，你需要同时按下 Alt-Escape 并输入 "console mode"。在控制台模式下，Alt-Tab 将具有前文描述中的功能，再次按下 Alt-Escape 会将您带回到图形化的 shell。

如果你想在终端模拟器中的图形化 shell 中使用文本 shell ，你可以通过选择 "Ask Anything" 框并输入 "moterm" 来启动 [moterm](https://fuchsia.googlesource.com/topaz/+/master/app/moterm)。

### 运行测试数据

构建的测试二进制文件安装在 `/system/test/` 中。
您可以通过在终端中调用它来运行测试。例如，

```
/system/test/ledger_unittests
```

如果你想离开 Fuchsia 运行，然后重新构建，并重新运行测试，请在一个终端上运行
启用了网络的 Fuchsia ，然后在另一个终端上运行：

```
fx run-test <test name> [<test args>]
```

## 贡献改变

* 请参阅 [CONTRIBUTING.md](CONTRIBUTING.md)。

## 其他有用的文档

* 使用Zircon - 复制文件，网络启动，日志查看，更多的在[这里](https://fuchsia.googlesource.com/zircon/+/master/docs/getting_started.md#Copying-files-to-and-from-Zircon)
* [Fuchsia文档](/ README.md) hub
* [构建variant](build_variants.md)，如杀毒软件以及 LTO。
* 建立[Fuchsia的工具链](toolchain.md)
* 更多关于[build命令](build_system.md)通过 `fx full-build` 来调用的内部函数
* 有关系统引导程序的更多信息
[这里](https://fuchsia.googlesource.com/application/+/HEAD/src/bootstrap/)。

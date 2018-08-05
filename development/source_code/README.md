
Fuchsia 源代码
==============


Fuchsia 使用 `jirl` 工具进行 git 版本管理 [https://fuchsia.googlesource.com/jiri](https://fuchsia.googlesource.com/jiri)。

其原理是通过一个 mainfest 文件来进行一整套的版本控制。关于如何构建 Fuchsia，请查阅 [Getting Started](/getting_started.md) 文档

## 创建一个新的 checkout

使用引导程序之前，请确保你的系统中已经安装了 Go 1.6 版本或以上的版本以及 Git ，并已将他们写入了系统环境变量。

首先，你需要选择系统中你想要构建的[layer](layers.md)。
(如果你不是那么确定，那就选择  `topaz` , `topaz` 包含更底层的 layers )
在这之后，运行下面的命令：


```
curl -s "https://fuchsia.googlesource.com/scripts/+/master/bootstrap?format=TEXT" | base64 --decode | bash -s <layer>
```

这个脚本将会在之前指定的 `layer` 目录的下自动建立所需要的环境文件。
在环境搭建成功的基础上，脚本界面将会推荐你添加  `.jiri_root/bin` 目录到你环境参数中。
我们强烈建议你添加 **jiri** 到系统的环境参数中，它可以作为 Fuchsia 其它部分的工具链使用。


### 在不更改环境变量的前提下使用

如果你不想对你的环境变量进行过多的更改，希望“仅仅运行” `jiri` 在你当前的目录下，
那么你可以简单的复制 `jiri` 到你的环境变量中。
这样做的前提是，你的使用账户对于 `jiri` 所在的**目录**必须**拥有写入权限**(不适用 `sudo`) 。
否则的话 `jiri` 将不能对自己进行更新。

```
cp .jiri_root/bin/jiri ~/bin
```

要使用 `fx` 工具，你也可以将它连接到你的 `~/bin` 目录之中。

```
ln -s `pwd`/scripts/fx ~/bin
```

或者直接使用 `scripts/fx` 来运行工具。请确保你已经把 **jiri** 添加到了你的环境变量中。
Multi Device Setup
============
多设备设置
============

This guide will walk you through the process of getting two Fuchsia devices
本指南将引导您完成获取两个Fuchsia设备的过程
set up and synchronizing story state using the
使用设置和同步story状态
[Ledger](https://fuchsia.googlesource.com/peridot/+/master/docs/ledger/).

## Setup
##设置

### Devices
### 设备

Follow the steps at in the [top level docs](README.md) to:
按照[top level docs](README.md)中的步骤操作
* Check out the source and build Fuchsia.
查看源程序并建立Fuchsia。
* Install it on two devices (or emulators).
在两个设备（或者模拟器）上安装它
* Connect the devices to the network.
将设备连接到网络。

### [Googlers only] Private Test Network Setup
###仅适用于[谷歌] 专用测试网络设置

Follow the instructions at [go/tq-net-boot](https://goto.google.com/tq-net-boot)
按照以下位置的说明操作[go/tq-net-boot](https://goto.google.com/tq-net-boot)
to set up a private test network.
设置专用测试网络。
### Identify Test Machines
###识别测试机器

Each Fuchsia device has a unique node name based on its MAC address.
每个Fuchsia设备基于其MAC地址具有唯一的节点名称。
It is of the form `power-nerd-saved-santa`.
它的形式`power-nerd-saved-santa`.
You can list the nodes on your network with the `netls` command.
您可以使用`netls`命令列出网络上的节点。

```
> netls
    device glad-bats-hunk-lady (fe80::f64d:30ff:fe68:2620/6)
    device blurt-chip-sugar-wish (fe80::8eae:4cff:feee:4f40/6)
```

### Running Commands On Test Machines
###在测试机器上运行命令

The `netruncmd <nodename> <command>` command can be used to run commands on remote machines.
`netruncmd <nodename> <command>`命令可用于在远程计算机上运行命令。
The output of the command is not shown.
未显示命令的输出。
If you need to see the output, use the `loglistener [<nodename>]` command.
如果需要查看输出，请使用 `loglistener [<nodename>]`命令。

### Ledger Setup
###Ledger设置

Ledger is a distributed storage system for Fuchsia.  
Ledger是一种分布式的Fuchsia存储系统。
Stories use it to synchronize their state across multiple devices.
Stories使用它跨多个设备同步它们的状态。
Follow the steps in Ledger's [User Guide](https://fuchsia.googlesource.com/peridot/+/master/docs/ledger/user_guide.md) to:
按照Ledger中的步骤操作[User Guide](https://fuchsia.googlesource.com/peridot/+/master/docs/ledger/user_guide.md)

* Set up [persistent storage](https://fuchsia.googlesource.com/zircon/+/master/docs/minfs.md). (optional)
建立[persistent storage](https://fuchsia.googlesource.com/zircon/+/master/docs/minfs.md)
* Verify the network is connected.
验证网络是否已连接。
* Configure a Firebase instance.
配置一个Firebase实例。
* Setup sync on each device using `configure_ledger`.
用命令`configure_ledger`在每个设备上同步设置

## Run Stories
## 运行Stories

### Virtual consoles.
### 虚拟控制台。

The systems boots up with three virtual consoles.
系统将启动三个虚拟控制台。  
Alt-F1 through Alt-F3 can be used to switch between virtual consoles.
可以用Alt-F1和Alt-F3来在两个虚拟控制台之间切换

### Wipe Data

The format of the Ledger as well as the format of the data each story syncs is under rapid development and no effort is currently made towards forwards and backwards compatibility.  
Ledger的格式以及每个story同步的数据格式正在迅速发展，目前没有实现前后兼容。
Because of this, after updating the Fuchsia code, it is a good idea to wipe your remote and local data using `cloud_sync clean`.
因此，在更新Fuchsia代码后，最好使用`cloud_sync clean`擦除远程和本地数据。

```
$ netruncmd <nodename> cloud_sync clean
```

### Start A Story On One Device
### 在一个设备上开始一个Story

Use the `device_runner` to start a story on one device:
使用`device_runner`在一个设备上开始story:

```
$ netruncmd <first-node-name> "device_runner --user_shell=dev_user_shell \
  --user_shell_args=--root_module=example_todo_story"
```

Using `loglistener <first-node-name>` take note of the story ID from a line the following:
使用`loglistener <first-node-name>`，从以下行记下story ID :

```
... DevUserShell Starting story with id: IM7U9hBcCt
	DevUserShell用id开启story：IM7U9hBcCt
```

### Open The Same Story On The Second Device.
### 在第二个设备上打开相同的story

The story can be started on the second device either through the system UI or by specifying the story ID.
story可以通过系统UI或通过指定story ID在第二个设备上启动。

#### System UI, aka User Shell, aka Armadillo
#### 系统用户界面，又名用户外壳，又名Armadillo
Launch the system UI using `device_runner`:
用命令`device_runner`登陆系统UI

```
$ netruncmd <second-node-name> "device_runner"
```

Once the system UI starts, you should be able to see the story started in the step above.  
一旦系统UI启动，您应该能够看到story开始于上面的步骤。
Click on that to open it.
点击以便打开它。

#### By Story ID
#### 通过Story ID

With the story ID noted above from launch the story from a shell:
上面提到的story ID是从shell中发布的story:

```
$ netruncmd <second-node-name> "device_runner \
  --user_shell=dev_user_shell \
  --user_shell_args=--story_id=<story_id>
```

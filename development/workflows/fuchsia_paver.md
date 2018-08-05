# Putting Fuchsia on a Device
#在一个设备上安装Fuchsia

One of the best ways to experience Fuchsia is by running it on actual hardware.
通过在实际硬件上运行Fuchsia是最好的体验方式之一。
This guide will help you get Fuchsia installed on your device.
本指南将帮助您在设备上安装Fuchsia。
 Fuchsia has good support for a few different hardware platforms including the Acer Switch 12,
Intel NUC, and Google Pixelbook (not to be confused with the Chromebook Pixel).
Fuchsia对一些不同的硬件平台有很好的支持，包括宏基交换机12，
英特尔核和谷歌Pixelbook(不要与Chromebook Pixel混淆)
The install process is not currently compatible with ARM-based targets. 
安装过程当前与基于ARM的目标不兼容。
The Fuchsia install process, called 'paving', requires two machines, the machine on which you want to run Fuchsia ("target") and the machine on which you build Fuchsia ("host"). 
Fuchsia安装过程称为'paving'，需要两台机器，一台是要运行Fuchsia的机器(“目标”)，另一台是要构建Fuchsia的机器(“主机”)。
Host and target must be able to communicate over a local area
network.
主机和目标必须能够通过局域网进行通信。
 On your host system you will build Fuchsia, create a piece of install media, and stream a large portion of the system over the network to the target.
 在您的主机系统上，您将构建Fuchsia，创建一个安装介质，并通过网络将系统的很大一部分传输到目标。

The `fx` command will be used throughout these instructions. 
这些说明将一直使用`fx`命令。
If you have fx mapped into your command path you can follow the instructions verbatim. 
如果已将fx映射到命令路径中，您可以一步步按照说明进行操作。
If you don't have fx in your path, it can be found at `//scripts/fx` and you'll need to use the appropriate relative path in the supplied commands. 
如果您的路径中没有FX，则可以在`//scripts/fx`中找到它，您需要在提供的命令中使用适当的相对路径。
Many of fx commands are relatively thin wrappers around build actions in GN coupled with tool invocations. 
许多fx命令都是围绕GN中的构建操作和工具调用相对较薄的封装器。
If your use case isn't quite served by what's currently
available there may a few GN targets you can build or some GN templates you can extend to allow you to build what you need.
如果您的用例通过可用的GN目标没有得到当前的服务，您可以构建，或者可以扩展一些GN模板，以允许您构建所需的内容。
## TL;DR

Read this all before? Here are the common case 
以前都读过吗？以下是常见的案例命令
1. `fx set x86-64`
2. `fx full-build`
3. Make the install media
3 .制作安装媒体
    * [[ insert USB drive into host ]]
    * [ [将USB驱动器插入主机] ]
    * `fx mkzedboot <usb_drive_device_path>`
4. Boot and pave
    * [[ move USB drive to target ]]
    * [ [将USB驱动器移至目标] ]
    * `fx boot <efi|vboot|nuc|cros|..>`

## Building
##建立

Detailed instructions for obtaining and building Fuchsia are available from the [Getting Started](getting_started.md) guide, but we'll assume here that the target system is x86-based and that you want to build a complete system. 
有关获取和构建Fuchsia的详细说明，请参阅 [Getting Started](getting_started.md) 指南，但我们将在此假定目标系统是基于x86的，并且您希望构建一个完整的系统。
To configure our build for this we can run `fx set x86-64` and then build with `fx full-build`.
要生成此配置，我们可以运行`fx set x86-64`，然后使用`fx full-build`进行生成。

## Creating install media
##创建安装介质

To create your install media we recommend using a USB drive since these are well-supported as boot media by most systems.
要创建安装介质，我们建议使用USB驱动器，因为大多数系统都非常支持USB驱动器作为引导介质。
Note that the install media creation process **will wipe everything** from the USB drive being used.
请注意，安装介质创建过程会从正在使用的USB中把一切都抹除掉。
Insert the USB drive and then run `fx mkzedboot <device_path>`, which on Linux is typically something like /dev/sd&lt;X&gt; where X is a letter and on Mac is typically something like /dev/disk&lt;N&gt; where 'N' is a number.
插入USB驱动器，然后运行`fx mkzedboot <device_path>`，这在Linux上通常类似于/dev/sd&lt;X&gt; 其中X是一个字母，Mac上的字母通常类似于/dev/disk&lt;N&gt;其中'N'是一个数字。
 **Be careful not to select the wrong device**. Once this is done, remove the USB drive.
注意不要选择错误的设备。完成此操作后，卸下USB驱动器。

## Paving

Now we'll build the artifacts to transfer over the network during the paving process.
现在，我们将构建构件，以便在铺设过程中通过网络传输。
What is transferred is dependent on the target device. For UEFI based systems (like Intel NUC or Acer Switch 12) our output target type is 'efi'.
传输的内容取决于目标设备。对于基于UEFI的系统（如英特尔NUC或宏基交换机12 )，我们的输出目标类型为'efi'。
For ChromeOS-based systems (like Pixelbook) that use vboot-format images, the target type is 'vboot'.
对于使用vboot格式图像的基于ChromeOS的系统(如Pixelbook )，目标类型为'vboot'。
To start the bootserver with the correct image set you can run
`fx boot <target_type>`, ie. `fx boot efi`.
要使用正确的映像集启动引导服务器，您可以运行`fx boot <target_type>`，即`fx boot efi`。
Insert the install media into the target device that you want to pave.
将安装介质插入要您想要铺设的目标设备。
The target device's boot settings may need to be changed to boot from the USB device and this is typically device-specific.
可能需要更改目标设备的引导设置以从USB设备引导，这通常是设备特定的。
For the guides listed below, **only** go through the steps to set the boot device, don't continue with any instructions on
creating install media.
对于下面列出的指南，仅完成设置引导设备的步骤，不要继续执行有关创建安装介质的任何说明。
* [Acer Switch Alpha 12](https://fuchsia.googlesource.com/zircon/+/master/docs/targets/acer12.md)
* [Intel NUC](https://fuchsia.googlesource.com/zircon/+/master/docs/targets/nuc.md)
* [Google Pixelbook](development/hardware/pixelbook.md)

Paving should occur automatically after the device is booted into Zedboot from the USB drive.
设备从USB驱动器引导到Zedboot后，应自动进行铺设。
After the paving process completes, the system should boot into the Zircon kernel.
铺设过程完成后，系统应开机进入Zircon kernel。
After paving, the whole system is installed on internal storage.
铺设后，整个系统将安装在内部存储器上。
At this point the USB key can be removed since the system has everything it needs stored locally.
此时，USB密钥可以被移除，因为系统本地具有所有需要的储存。
If you plan to re-pave frequently it may be useful to keep the
will happen automatically.
如果您打算频繁地重新铺设，保持自动铺设可能会很有用。
After the initial pave on UEFI systems that use Gigaboot, another option for re-paving is to press 'z' while in Gigaboot to select Zedboot.
在使用gigboot的UEFI系统上进行初始铺设后，重新铺设的另一个选项是在gigboot中按'z'选择Zedboot。
For vboot-based systems using the USB drive is currently the
only option for re-paving.
对于基于vboot的系统，使用USB驱动器是当前重新铺设的唯一选项。
In all cases the bootserver needs to have been started with `fx boot <target_type>`
在所有情况下，启动服务器都需要使用`fx boot <target_type>`启动

## Troubleshooting
##故障排除

In some cases paving may fail because you have a disk layout that is incompatible.
在某些情况下，由于磁盘布局不兼容，铺设可能会失败。
In these cases you will see a message that asks you to run
'install-disk-image wipe'. 
在这些情况下，您将看到一条消息，要求您运行安装磁盘映像擦除。
If it is incompatible because it contains an older Fuchsia layout put there by installer (vs the paver) you can fix this by killing the fx boot process on the host, switching to a different console (Alt+F3) on the target, and running `install-disk-image wipe`. 
如果它不兼容，因为它包含由安装程序(与铺设机)放置在那里的较旧的Fuchsia布局，则可以通过停止主机上的fx引导过程、切换到目标上的其他控制台(Alt+F3)并运行`install-disk-image wipe`来修复此问题。
Then reboot the target,re-run `fx boot <target_type>` on the host, and the pave should succeed.
然后重新引导目标，在主机上重新运行`install-disk-image wipe`，铺设将成功。
In some cases paving may fail on an Acer with some error indicating "couldn't find space in gpt". 
在某些情况下，宏碁的铺设作业可能会失败，并出现一些错误，表明“在GPT中找不到空间”。
In these cases (as long as you don't want to keep the other
OS, i.e. Windows, parts) run `lsblk` and identify the partition that isn't your USB (it shouldn't have RE in the columns).
在这些情况下(只要您不想让其他操作系统(即Windows、部件)）运行`lsblk`，并识别不是USB的分区(它不应该在列中)。
Identify the number in the first column for your partition (likely to be either 000 or 003).
标识分区的第一列中的编号(可能是000或003 )。
Then run `gpt init /dev/class/block/N` where N is the number previously identified. 
然后运行`gpt init /dev/class/block/N`，其中N是先前标识的编号。
This will clear all Windows partitions from the disk. Once this is done, reboot into zedboot and paving should work.
这将从磁盘中清除所有Windows分区。完成此操作后，重新启动到zedboot中，铺设应正常进行。

## Running without a persistent /data partition
## 在没有持久性/数据分区的情况下运行

It is possible to run the system without a persistent data partition.
可以在没有持久数据分区的情况下运行系统。
When this is done `/data` is backed by a RAM filesystem and thus the device is kind of running in an incognito mode. 
完成此操作后，`/data`由RAM文件系统支持，因此该设备以匿名模式运行。
To create a device without a persistent data partition you
`fx boot <target_type> --no-data`. 
要创建没有持久数据分区的设备`fx boot <target_type> --no-data`。
If the device already has a data partition, running paver this way will **not** remove it.
如果设备已经有数据分区，则以这种方式运行铺设机将不会将其移除。
To remove the persistent data partition, don't run bootserver, boot the device into Zedboot, switch to a
command line (Alt+F3), and run `install-disk-image wipe`. 
要删除持久性数据分区，请不要运行bootserver，将设备引导到Zedboot中，切换到命令行(Alt+F3)，并运行`install-disk-image wipe`。
Then reboot the device into Zedboot and start boot server with
`fx boot <target_type> --no-data`.
然后将设备重新引导到Zedboot中，并使用`fx boot <target_type> --no-data`启动引导服务器。

## Changing boot target (localboot, netboot, etc) default
## 更改引导目标(本地引导、网络引导等)默认值

For EFI-based systems, it is possible to change the default boot option of the system paved on the target between local booting and Zedboot for network booting.
对于基于EFI的系统，可以在本地引导和网络引导的Zedboot之间更改在目标上铺设的系统的默认引导选项。
By default the system boots locally with a 1-second delay in Gigaboot to allow you to select a different mode.
默认情况下，系统以1秒的延迟在本地启动gigboot，以允许您选择其他模式。
To change this default to Zedboot, supply the `--always_zedboot` option when calling your build command, for example `fx full-build --always_zedboot`.
要将此默认值更改为Zedboot，请在调用生成命令时提供`--always_zedboot`选项，例如`fx full-build --always_zedboot`。
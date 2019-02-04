# Fuchsia Build Information

# Fuchsia 构建信息

We collection metrics and error reports from devices in a few ways: Cobalt, feedback reports, crashpad crashes, manual reports from developers and QA.  Interpreting these signals requires knowing where they are generated from to varying levels of detail.  This document describes the places where version information about the system are stored for use in these types of reports.  Note that this information only applies to the base system - dynamically or ephemerally added software will not be included here.

我们通过多种手段从设备上收集度量指标数据和错误报告：Cobalt， 反馈报告，crashpad的崩溃信息，开发者提交的报告以及Q&A。解读这些信息需要确定它们的来源，以便基于不同层次的细节信息进行解读。本文档描述了在这些报告中系统版本信息的存储位置。需要注意的是这些信息仅适用于基础系统，并不适用于被动态添加或者短暂添加的软件。

To access this data, add the feature "build-info" to the [component manifest](../../the-book/package_metadata.md#component-manifest) of the component that needs to read these fields.

想要访问这些数据，需要向那些需要读取这些信息的组件的[组件清单](../../the-book/package_metadata.md#component-manifest)加入“build-info”特性。

# Product
## Location

# Product
## 位置

`/config/build-info/product`

## Description
String describing the product configuration used at build time.  Defaults to the value passed to “--products” in fx set.
Example: “garnet/products/default.gni”, “topaz/products/dashboard.gni”

## 描述

字符串，其描述了product在编译阶段所使用的配置信息。该值的默认值可以通过fx set的“--products“选项进行指定。
例如：”garnet/products/default.gni“，”topaz/products/dashboard.gni“。


# Board
## Location

# Board
## 位置

`/config/build-info/board`

## Description
String describing the board configuration used at build time to specify the target hardware.  Defaults to the value passed to “--boards” in fx set.
Example: “garnet/boards/x64.gni”

## 描述
字符串，其描述了board在编译阶段所使用的用于指定目标硬件设备的配置信息。该值的默认值可以通过fx set的“--boards”选项进行指定。
例如：“garnet/boards/x64.gni”

# Version
## Location

# 版本（Version）
## 位置
`/config/build-info/version`

## Description
String describing the version of the build.  Defaults to the same string used currently in ‘last-update’.  Can be overridden by build infrastructure to provide a more semantically meaningful version, e.g. to include the release train the build was produced on.

## 描述
字符串，其描述了该次构建的版本信息。该值的默认值与当前在“最近更新（last-update）”中使用的字符串相同。它可以被编译基础单元所提供的更加语义化的版本信息所替代，例如，包含本次构建生成所基于的发布序列的信息。

# Last-update
## Location

# 最近更新（Last-update）
## 位置

`/config/build-info/last-update`

## Description
String containing a timestamp of the most recent time ‘jiri update’ was executed.  Example: “2018-11-06T14:47:59-08:00”.

## 描述
字符串，其包含一个时间戳，记录了“jiri update”最近一次被执行的时间。
例如：“2018-11-06T14:47:59-08:00”。

# Snapshot
## Location:

# 快照（Snapshot）
## 位置

`/config/build-info/snapshot`

## Description
Jiri snapshot of the most recent ‘jiri update’

## 描述
最近一次执行“jiri update”的jiri快照。

# Kernel version
## Location:
Stored in vDSO.  Accessed through [`zx_system_get_version`]( https://fuchsia.googlesource.com/zircon/+/master/docs/syscalls/system_get_version.md)

Zircon revision computed during the kernel build process.

# 内核版本（Kernel version）
## 位置：

储存在vDSO中。通过[`zx_system_get_version`](https://fuchsia.googlesource.com/zircon/+/master/docs/syscalls/system_get_version.md)来访问。

Zircon版次信息在内核编译过程中生成。

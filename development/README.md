# Development
# 开发

This document is a top-level entry point to all of Fuchsia documentation related
to **developing** Fuchsia and software running on Fuchsia.

本文档是与**开发** Fuchsia 和在 Fuchsia 上运行的软件相关的所有 Fuchsia 文档的顶级入口。

## Developer workflow
## 开发者工作流

This sections describes the workflows and tools for building, running, testing
and debugging Fuchsia and programs running on Fuchsia.

此部分描述了用于构建、运行、测试、调试 Fuchsia 以及在 Fuchsia 上运行的软件的工作流和工具。

 - [Getting started](../getting_started.md) - **start here**. This document
   covers getting the source, building and running Fuchsia.
 - [Source code](source_code/README.md)
 - [Multiple device setup](workflows/multi_device.md)
 - [Pushing a package](workflows/package_update.md)
 - [Changes that span layers](workflows/multilayer_changes.md)
 - [Debugging](workflows/debugging.md)
 - [Tracing][tracing]
 - [Trace-based Benchmarking][trace_based_benchmarking]
 - [Build system](build/README.md)
 - [Workflow FAQ](workflows/workflow_faq.md)
 - [Testing FAQ](workflows/testing_faq.md)

 - [Getting started](../getting_started.md) - **从此处开始**. 这个文档
   涵盖了源，构建以及运行 Fuchsia 的相关内容
 - [Source code](source_code/README.md)
 - [Multiple device setup](workflows/multi_device.md)
 - [Pushing a package](workflows/package_update.md)
 - [Changes that span layers](workflows/multilayer_changes.md)
 - [Debugging](workflows/debugging.md)
 - [Tracing][tracing]
 - [Trace-based Benchmarking][trace_based_benchmarking]
 - [Build system](build/README.md)
 - [Workflow FAQ](workflows/workflow_faq.md)
 - [Testing FAQ](workflows/testing_faq.md)

## Languages
## 编程语言

 - [C/C++](languages/c-cpp/README.md)
 - [Dart](languages/dart/README.md)
 - [FIDL](languages/fidl/README.md)
 - [Go](languages/go/README.md)
 - [Rust](languages/rust/README.md)
 - [Flutter modules](languages/dart/mods.md) - how to write a graphical module
   using Flutter

 - [C/C++](languages/c-cpp/README.md)
 - [Dart](languages/dart/README.md)
 - [FIDL](languages/fidl/README.md)
 - [Go](languages/go/README.md)
 - [Rust](languages/rust/README.md)
 - [Flutter modules](languages/dart/mods.md) - 如何使用 Flutter 编写一个图形模块

## API

 - [System](api/system.md) - Rubric for designing the Zircon System Interface
 - [FIDL](api/fidl.md) - Rubric for designing FIDL protocols
 - [C](api/c.md) - Rubric for designing C library interfaces

 - [System](api/system.md) - 针对设计 Zircon 系统界面的专题文档
 - [FIDL](api/fidl.md) -针对设计 FIDL 协议的专题文档
 - [C](api/c.md) - 针对设计 C 库接口的专题文档

## SDK

 - [SDK](sdk/README.md) - information about developing the Fuchsia SDK

 - [SDK](sdk/README.md) - 关于开发 Fuchsia SDK 的信息


## Hardware
## 硬件

This section covers Fuchsia development hardware targets.

这一部分涵盖了 Fuchsia 开发硬件的目标。

 - [Acer Switch Alpha 12][acer_12]
 - [Intel NUC][intel_nuc] (also [this](hardware/developing_on_nuc.md))
 - [Pixelbook](hardware/pixelbook.md)

 - [Acer Switch Alpha 12][acer_12]
 - [Intel NUC][intel_nuc] (也可参见[此](hardware/developing_on_nuc.md))
 - [Pixelbook](hardware/pixelbook.md)

## Conventions
## 公约

This section covers Fuchsia-wide conventions and best practices.

本章节介绍了 Fuchsia 的公约和最佳实践。

 - [Layers](source_code/layers.md) - the Fuchsia layer cake, ie. how Fuchsia
   subsystems are split into a stack of layers
 - [Repository structure](source_code/layer_repository_structure.md) - standard way
   of organizing code within a Fuchsia layer repository
 - [Documentation standards](/best-practices/documentation_standards.md)

 - [Layers](source_code/layers.md) - Fuchsia layer cake,例如， Fuchsia 子系统是如何分成 layer 的堆栈。
 - [Repository structure](source_code/layer_repository_structure.md) - 在 Fuchsia layer 仓库中组织代码的标准方式
 - [Documentation standards](/best-practices/documentation_standards.md)

## Miscellaneous
## 其它

 - [CTU analysis in Zircon](workflows/ctu_analysis.md)
 - [Persistent disks in QEMU](workflows/qemu_persistent_disk.md)

 - [CTU analysis in Zircon](workflows/ctu_analysis.md)
 - [Persistent disks in QEMU](workflows/qemu_persistent_disk.md)

[acer_12]: https://fuchsia.googlesource.com/zircon/+/master/docs/targets/acer12.md "Acer 12"
[intel_nuc]: https://fuchsia.googlesource.com/zircon/+/master/docs/targets/nuc.md "Intel NUC"
[pixelbook]: hardware/pixelbook.md "Pixelbook"
[tracing]: https://fuchsia.googlesource.com/garnet/+/master/docs/tracing_usage_guide.md
[trace_based_benchmarking]: benchmarking/trace_based_benchmarking.md

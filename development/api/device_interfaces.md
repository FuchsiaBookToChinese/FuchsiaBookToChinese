# Fuchsia Device Interface Rubric
# Fuchsia 设备接口注释

The Fuchsia device interfaces are expressed as FIDL interfaces.  These FIDL
definitions should conform to the [FIDL Readability Rubric].

Fuchsia 设备接口按照 FIDL 接口的方式表达。这些 FIDL 定义应当符合 [FIDL 可读性注释]。


## Identifiers
## 标识符

Prefer descriptive identifiers.  If you are using domain-specific abbreviations,
document the expansion or provide a reference for further information.

优先使用描述性标识符。如果你使用了特定领域的缩写术语，请记录完整术语或为此提供进一步的参考信息。

Every identifier that is defined as part of an interface must be documented with
a comment explaining its interpretation (in the case of fields, types, and
parameters) or behavior (in the case of methods).

每个被定义为接口一部分的标识符，必须要添加解释其演绎(在字段、类型、参数的情况下)或行为(在方法的情况下)的注释。

## Interfaces
## 接口

All device interfaces must use the `[Layout = "Simple"]` attribute.  This
restriction exists to allow ease of implementing interfaces in any of our
supported languages for driver development.

所有的设备接口必须使用 `[Layout = "Simple"]` 属性。这个限制的存在是为了便于在驱动开发在任何
我们支持的语言中实现接口

## Method Statuses
## 方法状态

Use a `zx.status` return to represent success and failure.  If a method should not be
able to fail, do not provide a `zx.status` return.  If the method returns multiple
values, the `zx.status` should come first.

使用一个 `zx.status` return 来代表成功或者失败。如果一个方法不会失败，那么就不要提供 `zx.status` return 。
如果一个方法会返回多个值，那么应该先返回 `zx.status` 。

## Arrays, Strings, and Vectors
## 数组，字符串，以及向量

All arrays, strings, and vectors must be of bounded length.  For arbitrarily
selected bounds, prefer to use a `const` identifier as the length so that
interface consumers can programmatically inspect the length.

所有的数组，字符串以及向量必须限制长度。对于任意选择的限制，首选使用 `const` 标识符作为长度，
以便接口使用者可以以编程方式检查长度。

## Enums
## 枚举

Prefer enums with explicit sizes (e.g. `enum Foo : uint32 { ... }`) to plain
integer types when a field has a constrained set of non-arithmetic values.

当字段具有一组约束的非算术值时，首选具有显式大小的枚举(例如`enum Foo：uint32 {...}`)
作为普通整数类型。

## Bitfields
## 位域

If your interface has a bitfield, represent its values using `const` values.
They should be grouped together in the FIDL file and have a common prefix.  For
example:

如果你的接口包含一个位域，使用 `const` 值来代表它的值。它们应该在 FIDL 文件中组合在
一起并具有公共前缀。例如：

```
// Bit definitions for Info.features field

// If present, this device represents WLAN hardware.
const uint32 INFO_FEATURE_WLAN = 0x00000001;
// If present, this device is synthetic (i.e. not backed by hardware).
const uint32 INFO_FEATURE_SYNTH = 0x00000002;
// If present, this device will receive all messages it sends.
const uint32 INFO_FEATURE_LOOPBACK = 0x00000004;
```

If FIDL gains bitfield support, this guidance will be updated.
如果未来 FIDL 获得位域支持，则将更新此指南。


## Non-channel based protocols
## 非信道协议

Some interfaces may negotiate a non-channel protocol as a performance
optimization (e.g. the zircon.ethernet.Device's GetFifos/SetIOBuffer methods).
FIDL does not currently support expressing these protocols.  For now, represent
any shared data structures with `struct` definitions and provide detailed
documentation about participation in the protocol.  Packed structures are not
currently supported.

一些接口可能会使用非信道协议作为性能优化(例如，zircon.ethernet.Device's GetFifos/SetIOBuffer 方法)。
FIDL 暂时不支持表达这些协议。目前为止，应使用 `struct` 定义来表达任意的共享数据结构，并提供有关参与的协议的详细文档。
打包结构目前也未支持。


[FIDL Readability Rubric]: fidl.md
[FIDL 可读性量规]: fidl.md
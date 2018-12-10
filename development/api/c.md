# C Library Readability Rubric
# C 库可读性量规库

This document describes heuristics and rules for writing C libraries
that are published in the Fuchsia SDK.

此文档描述了编写发布在 Fuchsia SDK 中的 C 库的启发和规则。

A different document will be written for C++ libraries. While C++ is
almost an extension of C, and has some influence in this document, the
patterns for writing C++ libraries will be quite different than for C.

针对 C++ 库将编写一个不同的文档。虽然 C++ 几乎是 C 的扩展，并且在本文档中产生了
一些影响，但编写 C++ 库的模式将与 C 完全不同。

Most of this document is concerned with the description of an
interface in a C header. This is not a full C style guide, and has
little to say about the contents of C source files. Nor is this a
documentation rubric (though public interfaces should be well
documented).

本文档的大部分内容涉及 C header 中接口的描述。但这不是完整的 C 风格指南，
对 C 源文件的内容也几乎没有涉及。这也不是一个文档量规（尽管公共接口应该有
很好的文档记录）。

Some C libraries have external constraints that contradict these
rules. For instance, the C standard library itself does not follow
these rules. This document should still be followed where applicable.

一些 C 库在外部拥有与这些规则相冲突的约束。例如，C 标准库自身就不会遵循这些规则。
在适用的情况下，仍应遵循这些规则。

[TOC]

## Goals
## 目标

### ABI Stability
### ABI 稳定性

Some Fuchsia interfaces with a stable ABI will be published as C
libraries. One goal of this document is to make it easy for Fuchsia
developers to write and to maintain a stable ABI. Accordingly, we
suggest not using certain features of the C language that have
potentially surprising or complicated implications on the ABI of an
interface. We also disallow nonstandard compiler extensions, since we
cannot assume that third parties are using any particular compiler,
with a handful of exceptions for the DDK described below.

一些包含了稳定 ABI 的 Fuchsia 接口将以 C 库的形式发布。此文档的目的之一是让
Fuchsia 开发者更加方便地编写以及维护稳定的 ABI。相应的，我i们建议不要使用 C 
语言的某些功能，这些功能可能会对接口的 ABI 产生令人惊讶的或是复杂的影响。我们
同样也不允许非标准的编译器插件，因为我们不能假设第三方正在使用任何特定的编译器
，但下面描述的 DDK 有少数例外。

### Resource Management
### 资源管理

Parts of this document describe best practices for resource management
in C. This includes resources, Zircon handles, and any other type of
resource.

此文档的一部分描述了在 C 中资源管理的最佳实践。这包括了 资源， Zircon handles 以及
任何其它类型的资源。

### Standardization
### 标准化

We would also like to adopt reasonably uniform standards for Fuchsia C
libraries. This is especially true of naming schemes. Out parameter
ordering is another example of standardization.

我们同样希望为 Fuchsia C 库采取合理统一的标准，对于命名方案尤其如此。
Out 参数排序是标准化的另一个例子。

### FFI Friendliness
### FFI 友好

Some amount of attention is paid to Foreign Function Interface (FFI)
friendliness. Many non-C languages support C interfaces. The
sophistication of these FFI systems varies wildly, from essentially
sed to sophisticated libclang-based tooling. Some amount of
consideration of FFI friendliness went into these decisions.

外部功能接口(FFI）友好性受到一定程度的关注。许多非 C 语言支持 C 接口。
从基本的 sed 到基于 libclang 的复杂工具,这些 FFI 系统的复杂程度差异很大，
这些决定融入了一些对 FFI 友好的考量。

## Language versions
## 语言版本

### C

Fuchsia C libraries are written against the C11 standard (with a small
set of exceptions, such as unix signal support, that are not
particularly relevant to our C library ABI). C99 compliance is not a
goal.

Fuchsia C库是根据C11标准编写的(有一些小的例外，例如 unix signal 支持，
这与我们的 C 库 ABI 并不特别相关)。C99 合规不在考虑范围内。

In particular, Fuchsia C code can use the `<threads.h>` and
`<stdatomic.h>` headers from the C11 standard library, as well as the
`_Thread_local` and alignment language features.

通常，Fuchsia C 代码可以使用 C11 标准库中的 `<threads.h>` 以及 `<stdatomic.h>` 
头文件，以及 `_Thread_local` 和对齐语言特性。

The thread locals should use the `thread_local` spelling from
`<threads.h>`, rather than the built in `_Thread_local`. Similarly,
prefer `alignas` and `alignof` from `<stdalign.h>`, rather than
`_Alignas` and `_Alignof`.

thread locals 应当使用 `<threads.h>` 中的 `thread_local` 拼写，
而不是内置的 `_Thread_local`。同样的，优先使用 `<stdalign.h>` 中的
`alignas` 和 `alignof`，而不是 `_Alignas` 和 `_Alignof`。

Note that compilers support flags which may alter the ABI of the
code. For instance, GCC has a `-m96bit-long-double` flag which alters
the size of a long double. We assume that such flags are not used.

需要注意的是编译器支持可能会改变代码的 ABI 的 flags。例如， GCC 可通过 
`-m96bit-long-double` flag 来改变 long double 的大小。我们假定这样的 flag 
不会被使用。

Finally, some libraries (such as Fuchsia's C standard library) in our
SDK are a mix of externally defined interfaces and Fuchsia specific
extensions. In these cases, we allow some pragmatism. For instance,
libc defines functions like `thrd_get_zx_handle` and
`dlopen_vmo`. These names are not strictly in accordance with the
rules below: the name of the library is not a prefix. Doing so would
make the names fit less well next to other functions like
`thrd_current` and `dlopen`, and so we allow the exceptions.

最后，一些在我们 SDK 中的库(例如 Fuchsia 的 C 标准库)是外部定义的接口和 Fuchsia
特定扩展的混合。在这些情况下，我们允许一些实用主义的思路。例如，libc 定义了一些类似
`thrd_get_zx_handle` 和 `dlopen_vmo` 的函数。这些名称并不是严格按照下面的规则的：
库的名称不是一个前缀。这样做会使得名称与其它函数(例如 `thrd_current` 和 `dlopen`)
的匹配性降低，所以我们允许异常的发生。

### C++
### C++

While C++ is not an exact superset of C, we still design C libraries
to be usable from C++. Fuchsia C headers should be compatible with the
C++11, C++14, and C++17 standards. In particular, function
declarations must be `extern "C"`, as described below.

尽管 C++ 不是 C 的精确超集，我们仍然设计 C 库可以从 C++ 中使用。Fuchsia C 的
头文件应当与 C++11, C++14 以及 C++17 标准兼容。通常情况下，函数声明必须是 
`extern "C"` 的，详细的描述见下。

C and C++ interfaces should not be mixed in one header. Instead,
create a separate `cpp` subdirectory and place C++ interfaces in their
own headers there.

C 和 C++ 的接口不应当混写一个头文件里。相对的，创建一个分开的 `cpp` 子目录并将
C++ 接口放入它们各自的头文件中。

## Library Layout and Naming
## 库布局和命名

A Fuchsia C library has a name. This name determines its include path
(as described in the [library naming document]) as well as identifiers
within the library.

Fuchsia C 库拥有一个名称。这个名称明确了其包含的路径(再 [library naming 
document] 中有所描述) 以及库中的标识符。

In this document, the library is always named `tag`, and is variously
referred to as `tag` or `TAG` or `Tag` or `kTag` to reflect a
particular lexical convention. The `tag` should be a single identifier
without underscores. The all-lowercase form of a tag is given by the
regular expression `[a-z][a-z0-9]*`.  A tag can be replaced by a shorter
version of the library name, for example `zx` instead of `zircon`.

在此文档中，库通常命名为 `tag`，同样也可被命名为 `tag`，`TAG`，`Tag` 或 `KTag`
以反映特定的词汇习惯。`tag` 应当是没有下划线的单一标识符。全小写格式的 tag 应当来
自正则表达书 `[a-z][a-z0-9]*`。tag 可以被缩写的库名称所替换，例如用 `zx` 替换 
`zircon`。 

The include path for a header `foo.h`, as described by the [library
naming document], should be `lib/tag/foo.h`.

如 [library naming document] 所描述,头文件 `foo.h` 的包含路径应当是 `lib/tag/foo.h`。

## Header Layout
## 头文件布局

A single header in a C library contains a few kinds of things.
C 库中独立的头文件包含一下这些东西。

- A copyright banner
- A header guard
- A list of file inclusions
- Extern C guards
- Constant declarations
- Extern symbol declarations
  - Including extern function declarations
- Static inline functions
- Macro definitions

- copyright banner
- 头文件防范
- 包含文件列表
- 外部 C 防范
- 一致性声明
- 外部符号声明
  - 包含外部函数声明
- 静态内联函数
- 宏定义

### Header Guards
### 头文件防范

Use #ifndef guards in headers. These look like:
在头文件中使用 #ifndef 防范。例子如下：

```C
#ifndef SOMETHING_MUMBLE_H_
#define SOMETHING_MUMBLE_H_

// code
// code
// code

#endif // SOMETHING_MUMBLE_H_
```

The exact form of the define is as follows:
- Take the canonical include path to the header
- Replace all ., /, and - with _
- Convert all letters to UPPERCASE
- Add a trailing _

确切的定义形式如下：
- 在头文件中采用标准的包含路径
- 用 _ 替换所有的 . / 和 - 
- 将所有的字母转为大写
- 添加尾部 _


For example, the header located in the SDK at `lib/tag/object_bits.h`
should have a header guard `LIB_TAG_OBJECT_BITS_H_`.

例如，头文件在 SDK 中位于 `lib/tag/object_bits.h` 应当以 `LIB_TAG_OBJECT_BITS_H_` 
作为头文件防范。

### Inclusions
### 包含

Headers should include what they use. In particular, any public header
in a library should be safe to include first in a source file.

Libraries can depend on the C standard library headers.

Some libraries may also depend on a subset of POSIX headers. Exactly
which are appropriate is pending a forthcoming libc API review.

头文件应当包含它们使用的内容。通常，库中任何的公共头文件被首先包含在源文件中应当
是安全的。

库可以依赖于 C 标准库头文件。

一些库也可坑依赖于 POSIX 头文件的子集。确切地说，哪些是合适的取决于即将到来的 
libc API 审查。

### Constant Definitions
### 常数定义

Most constants in a library will be compile-time constants, created
via a `#define`. There are also read-only variables, declared via
`extern const TYPE NAME;`, as it sometimes is useful to have storage
for a constant (particularly for some forms of FFI). This section
describes how to provide compile time constants in a header.

大部分库中的常数是编译时常量，通过 `#define` 创建。还有只读变量，通过
`extern const TYPE NAME;` 声明，它有时对常量储存是很有用的(尤其是对某些形
式的 FFI)。这部分的章节描述了如何在头文件中提供编译时常量。

There are several types of compile time constants.

编译时常量有以下几种类型。

- Single integer constants
- Enumerated integer constants
- Floating point constants

- 单个整数常量
- 枚举整数常量
- 浮点常量

#### Single integer constants
#### 单个整数常量

A single integer constants has some `NAME` in a library `TAG`, and its
definition looks like the following.

单个整数常量在`TAG` 库中有一些 `NAME`，其定义与如下相似。

```C
#define TAG_NAME EXPR
```

where `EXPR` has one of the following forms (for a `uint32_t`)

其中 `EXPR` 具有以下形式之一（对于`uint32_t`)

- `((uint32_t)23)`
- `((uint32_t)0x23)`
- `((uint32_t)(EXPR | EXPR | ...))`

#### Enumerated integer constants
#### 枚举整数常量

Given an enumerated set of integer constants named `NAME` in a library
`TAG`, a related set of compile-time constants has the following parts.

First, a typedef to give the type a name, a size, and a
signedness. The typedef should be of an explicitly sized integer
type. For example, if `uint32_t` is used:


给定 `TAG` 库中名为 `NAME` 的枚举整数常量集，一组相关的编译时常量具有以下部分。

首先，typedef 给定 type 一个名称，一个大小，以及一个符号类型。typedef 应该是显
式大小的整数类型。例如，如果使用了 `uint32_t`：

```C
typedef uint32_t tag_name_t;
```

Each constant then has the form

然后每个常量都有如下格式

```C
#define TAG_NAME_... EXPR
```

where `EXPR` is one of a handful of types of compile-time integer
constants (always wrapped in parentheses):

其中 `EXPR` 是少数类型的编译时整数常量之一（总是用括号括起来）：

- `((tag_name_t)23)`
- `((tag_name_t)0x23)`
- `((tag_name_t)(TAG_NAME_FOO | TAG_NAME_BAR | ...))`

Do not include a count of values, which is difficult to maintain as
the set of constants grows.

不要包含值的计数，当常量集增长时这将难以维护。

#### Floating point constants
#### 浮点常量

Floating point constants are similar to single integer constants,
except that a different mechanism is used to describe the type. Float
constants must end in `f` or `F`; double constants have no suffix;
long double constants must end in `l` or `L`. Hexadecimal versions of
floating point constants are allowed.

除了需要使用不同的机制来描述类型，浮点常量与单个整数常量几乎相同。浮点常量必须
以 `f` 或 `F` 结尾; 双常数没有后缀; long double 常量必须以 `l` 或 `L` 结尾。
允许使用十六进制版本的浮点常量。

```C
// A float constant
#define TAG_FREQUENCY_LOW 1.0f

// A double constant
#define TAG_FREQUENCY_MEDIUM 2.0

// A long double constant
#define TAG_FREQUENCY_HIGH 4.0L
```

### Function Declarations
### 函数声明

Function declarations should all have names beginning with `tag_...`.

函数声明的命名需要以 `tag_...` 开头。

Function declarations should be placed in `extern "C"` guards. These
are canonically provided by using the `__BEGIN_CDECLS` and
`__END_CDECLS` macros from [compiler.h].

函数声明应当放置在 `extern "C"` 防范中。这些是用 [compiler.h] 中的 `__BEGIN_DECLS` 
和 `__END_CDECLS` 宏规范提供的.

#### Function parameters
#### 函数参数

Function parameters must be named. For example,
函数参数必须被命名。例如，

```C
// Disallowed: missing parameter name
zx_status_t tag_frob_vmo(zx_handle_t, size_t num_bytes);

// Allowed: all parameters named
zx_status_t tag_frob_vmo(zx_handle_t vmo, size_t num_bytes);
```

It should be clear which parameters are consumed and which are
borrowed. Avoid interfaces in which clients may or may not own a
resource after a function call. If this is infeasible, consider noting
the ownership hazard in the name of the function, or one of its
parameters. For example:

需要明确哪些参数是被耗用的，哪些参数是被借用的。避免客户端在函数调用后
可能拥有或未拥有资源的接口。如果这是不可行的，考虑在函数名称或其参数之
一中标注警告所有权风险。 例如：

```C
zx_status_t tag_frobinate_subtle(zx_handle_t foo);
zx_status_t tag_frobinate_if_frobable(zx_handle_t foo);
zx_status_t tag_try_frobinate(zx_handle_t foo);
zx_status_t tag_frobinate(zx_handle_t maybe_consumed_foo);
```

By convention, out parameters go last in a function's signature, and
should be named `out_*`.

按照惯例，out 参数在函数的签名中排在最后，并应该被命名为 `out_ *`。

#### Variadic functions
#### 变量函数

Variadic functions should be avoided for everything except printf-like
functions. Those functions should document their format string
contract with the `__PRINTFLIKE` attribute from [compiler.h].

除了类似 printf 的函数之外，应避免使用变量函数。这些函数应该使用 
[compiler.h] 中的 `__PRINTFLIKE` 属性记录它们的格式字符串合约。

#### Static inline functions
#### 静态内联函数

Static inline functions are allowed, and are preferable to
function-like macros. Inline-only (that is, not also `static`) C
functions have complicated linkage rules and few use cases.

允许使用静态内联函数，并且它比类似函数的宏更可取。仅内联(即也非 `static`) 
C函数具有复杂的链接规则和极少的用例。

### Types
### 类型

Prefer explicitly sized integer types (e.g. `int32_t`) to
non-explicitly sized types (e.g. `int` or `unsigned long int`). An
exemption is made for `int` when referring to POSIX file descriptors,
and for typedefs like `size_t` from the C or POSIX headers.

优先使用显式大小的整数类型(例如`int32_t`）而非非显式大小的类型
(例如 `int` 或 `unsigned long int`)。但当引用 POSIX 文件描述符时，
对 `int` 和来自 C 或 POSIX 头文件的 `size_t` 类型的 typedef 可以不严格
遵守上述规则。

When possible, pointer types mentioned in interfaces should refer to
specific types. This includes pointers to opaque structs. `void*` is
acceptable for referring to raw memory, and to interfaces that pass
around opaque user cookies or contexts.

如果可能，接口中提到的指针类型应该引用特定类型。 这包括指向不透明结构体的指针。
 `void *` 可用于引用原始内存，以及传递不透明用户的 cookie 或上下文的接口。

#### Opaque/Explicit types
#### 不透明/显式类型

Defining an opaque struct is preferable to using `void*`. Opaque
structs should be declared like:

定义不透明的结构体比使用 `void *` 更可取。不透明结构应体声明如下：

```C
typedef struct tag_thing tag_thing_t;
```

Exposed structs should be declared like:

暴露的结构体应声明如下：

```C
typedef struct tag_thing {
} tag_thing_t;
```

#### Reserved fields
#### 保留字段

Any reserved fields in a struct should be documented as to the purpose
of the reservation.

结构体中的任何保留字段都应以保留为目的记录。

A future version of this document will give guidance as to how to
describe string parameters in C interfaces.

本文档的未来版本将提供有关如何在 C 接口中描述字符串参数的指导。

#### Anonymous types
#### 匿名类型

Top-level anonymous types are not allowed. Anonymous structures and
unions are allowed inside other structures, and inside function
bodies, as they are then not part of the top level namespace. For
instance, the following contains an allowed anonymous union.

高等级的匿名类型是不被允许的，匿名结构体(structures)以及共用体(unions)被允
许出现在其它结构体以及函数体中，因为它们不是顶级命名空间的一部分。例如，以下
包含了允许的匿名共用体。

```C
typedef struct tag_message {
    tag_message_type_t type;
    union {
        message_foo_t foo;
        message_bar_t bar;
    };
} tag_message_t;
```

#### Function typedefs
#### 函数 typedef

Typedefs for function types are permitted.

允许使用函数类型的 Typedef。

Functions should not overload return values with a `zx_status_t` on
failure and a positive success value. Functions should not overload
return values with a `zx_status_t` that contains additional values not
described in [zircon/errors.h].

函数不应该重载返回值，包括在失败时返回一个‘zx_status_t’，并且返回一个正的成功值。
函数不应该使用包含 [zircon/errors.h] 中未描述的其它值的 `zx_status_t` 来重载返
回值。


#### Status return
#### 状态返回

Prefer `zx_status_t` as a return value to describe errors relating to
Zircon primitives and to I/O.

首选 `zx_status_t` 作为返回值来描述与 Zircon 基元和 I/O 相关的错误。

## Resource Management
## 资源管理

Libraries can traffic in several kinds of resources. Memory and Zircon
handles are examples of resources common across many
libraries. Libraries may also define their own resources with
lifetimes to manage.

库可以管理多种资源。内存和 Zircon handles 是许多库中常见的资源示例。库
还可以定义自己的资源，并确定管理的生命周期。

Ownership of all resources should be unambiguous. Transfer of
resources should be explicit in the name of a function. For example,
`create` and `take` connote a function transferring ownership.

所有资源的所有权应该是明确的。资源的转移应该在函数名称中明确。例如，
`create` 和 `take` 意味着一个转移所有权的函数。

Libraries should be memory tight. Memory allocated by a function like
`tag_thing_create` should released via `tag_thing_destroy` or some
such, not via `free`.

库应当是内存紧缩的。由 `tag_thing_create` 这样的函数分配的内存应该通过 
`tag_thing_destroy` 或其他一些函数释放，而不是通过 `free` 释放。

Libraries should not expose global variables. Instead, provide
functions to manipulate that state. Libraries with process-global
state must be dynamically linked, not statically. A common pattern is
to split a library into a stateless static part, containing almost all
of the code, and a small dynamic library holding global state.

库不应暴露全局变量。相反，应当提供操作该状态的函数。具有进程全局状态的库必须动
态链接，而非静态链接。一种常见的模式是将库拆分为包含几乎所有代码的无状态静态部
分，以及包含全局状态的小型动态库。

In particular, the `errno` interface (which is a global thread-local
global) should be avoided in new code.

在新代码中尤其应该避免使用 `errno` 接口(它是一个全局线程局部全局)。

## Linkage
## 连锁

The default symbol visibility in a library should be hidden. Use
either a whitelist of exported symbols, or explicit visibility
annotations, on symbols to exported.

C libraries must not export C++ symbols.

应当隐藏库中的默认符号可见性。在要导出的符号上使用导出符号的白名单或显
式可见性注释。

C 库不得导出 C++ 符号。

## Evolution
## 发展进程

### Deprecation
### 弃用

Deprecated functions should be marked with the __DEPRECATED attribute
from [compiler.h]. They should also be commented with a description
about what to do instead, and a bug tracking the deprecation.

弃用的函数应当使用 [compiler.h] 的 __DEPRECATED 属性进行标记。同时它们还
应当附上该如何做的说明以及跟踪弃用的 bug。

## Disallowed or Discouraged Language Features
## 不允许以及不鼓励的语言特性

This section describes language features that cannot or should not be
used in the interfaces to Fuchsia's C libraries, and the rationales
behind the decisions to disallow them.

这部分描述了在 Fuchsia 的 C 库中的接口不能以及不应当使用的语言特性，以及在不使用
它们的这个决定的背后的原因和根据。

### Enums
### 枚举

C enums are banned. They are brittle from an ABI standpoint.

C 枚举是禁止的。从 ABI 标准来看它们是不稳定的。

- The size of integer used to represent a constant of enum type is
  compiler (and compiler flag) dependent.
- The signedness of an enum is brittle, as adding a negative value to
  an enumeration can change the underlying type.

 - 用于表示枚举类型常量的整数大小取决于编译器(和编译器标志)。
 - 枚举的签名是不稳定的，因为向枚举添加负值可以更改基础类型。

### Bitfields
### 位域

C's bitfields are banned. They are brittle from an ABI standpoint, and
have a lot of nonintuitive sharp edges.

Note that this applies to the C language feature, not to an API which
exposes bit flags. The C bitfield feature looks like:

C 的位域是被禁止的。从 ABI 标准来看它们是不稳定的，并且有许多反直觉的 sharp edges。

请注意，这适用于 C 语言特性，而不适用于公开位标志的 API。 C 位域功能如下所示：

```C
typedef struct tag_some_flags {
    // Four bits for the frob state.
    uint8_t frob : 4;
    // Two bits for the grob state.
    uint8_t grob : 2;
} tag_some_flags_t;
```

We instead prefer exposing bit flags as compile-time integer
constants.

我们更建议将位标志暴露为编译时整数常量。

### Empty Parameter Lists

C allows for function `with_empty_parameter_lists()`, which are
distinct from `functions_that_take(void)`. The first means "take any
number and type of parameters", while the second means "take zero
parameters". We ban the empty parameter list for being too dangerous.

C 允许与 `functions_that_take(void)` 不同的函数 `with_empty_parameter_lists()`。
首先这意味着“采用任何数量和类型的参数”，其次意味着“采用零参数”。 我们禁止空参数列表
是因为使用它存在过多风险。

### Flexible Array Members
### 柔性数组成员

This is the C99 feature which allows declaring an incomplete array as
the last member of a struct with more than one parameter. For example:

这是 C99 中的特性，允许在结构体的的末尾定义一个拥有多个参数的非完整数列。例如：

```C
typedef struct foo_buffer {
    size_t length;
    void* elements[];
} foo_buffer_t;
```

As an exception, DDK structures are allowed to use this pattern when
referring to an external layout that fits this header-plus-payload
pattern.

作为例外，当引用符合 header-plus-payload 模式的外部布局时，允许 DDK 结构
体使用此模式。

The similar GCC extension of declaring a 0-sized array member is
similarly disallowed.

同样，也不允许声明 0 大小数组成员的类 GCC 扩展。

### Module Maps
### 模块地图

These are a Clang extension to C-like languages which attempt to solve
many of the issues with header-driven compilation. While the Fuchsia
toolchain team is very likely to invest in these in the future, we
currently do not support them.

这些是类 C 语言的 Clang 扩展，它试图通过头驱动编译来解决许多问题。虽然 Fuchsia 
工具栏团队很可能在未来引入这些特性，但目前我们并不支持它们。

### Compiler Extensions
### 编译器扩展

These are, by definition, not portable across toolchains.

根据定义，这些编译器扩展是不可跨工具链移植的。

This in particular includes packed attributes or pragmas, with one
exception for the DDK.

特别的，它还包括打包属性或编译指示，但对于 DDK 有一个例外。

DDK structures often reflect an external layout that does not match
the system ABI. For instance, it may refer to an integer field that is
less aligned than required by the language. This can be expressed via
compiler extensions such as pragma pack.

DDK 结构体常常反映外部的不符合系统 ABI 的布局。例如，它可能涉及一个比语言标准
更少对齐的整数域。这可以通过编译器扩展来表达，例如 pragma pack。

[compiler.h]: https://fuchsia.googlesource.com/zircon/+/master/system/public/zircon/compiler.h
[library naming document]: ../languages/c-cpp/naming.md

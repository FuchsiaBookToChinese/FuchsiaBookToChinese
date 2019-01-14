# Fuchsia Build System: Variants

# Fuchsia构建系统：Variants

The Fuchsia GN build machinery allows for separate components to be built
in different "variants".  A variant usually just means using extra compiler
options, but they can do more than that if you write some more GN code.
The variants defined so far enable things like
[sanitizers](https://github.com/google/sanitizers/wiki) and
[LTO](https://llvm.org/docs/LinkTimeOptimization.html).

Fuchsia的GN构建系统允许不同的组件在不同的“Variants”下进行构建。
Variant通常仅仅意味着使用额外的编译器选项，但是如果结合GN代码进行使用则可以实现更多的功能。
目前定义的variant提供了一些功能，比如[sanitizers](https://github.com/google/sanitizers/wiki) 和 
[LTO](https://llvm.org/docs/LinkTimeOptimization.html).

The GN build argument
[`select_variant`](https://fuchsia.googlesource.com/garnet/+/master/docs/gen/build_arguments.md#select_variant)
controls which components are built in which variants.  It applies
automatically to every `executable`, `loadable_module`, or `driver_module`
target in GN files.  It's a flexible mechanism in which you give a list of
matching rules to apply to each target to decide which variant to use (if
any).  To support this flexibility, the value for `select_variant` uses a
detailed GN syntax.  For simple cases, this can just be a list of strings.

GN的构建参数
[`select_variant`](https://fuchsia.googlesource.com/garnet/+/master/docs/gen/build_arguments.md#select_variant)
指定了哪些组件在哪些variants下被构建。
该选项会被自动应用于GN文件中的每个`executable`，`loadable_module`和`driver_module`目标上。
这是一个弹性化机制，如果存在多个variant的话，你可以为每个目标提供一个匹配规则列表用以决定使用哪一个variant。
为了实现这种灵活性，`select_variant`参数使用了一种比较复杂的GN语法。
对于简单的情况，这个参数可以只是一个字符串列表。

Here's an example running `gn gen` directly:

下面的例子直接运行了命令`gn gen`：

```sh
./buildtools/gn gen out/x64 --args='select_variant=["host_asan", "asan/cat", "asan/ledger"]'
```

This does the same thing using the `fx set` tool:

下面的命令使用了`fx set`工具实现了与上一个例子相同的功能：

```sh
fx set x64 --variant={host_asan,asan/cat,asan/ledger}
```

 1. The first switch applies the `host_asan` matching rule, which enables
    [AddressSanitizer](https://clang.llvm.org/docs/AddressSanitizer.html)
    for all the executables built to run on the build host.

 2. The second switch applies the `asan` matching rule, which enables
    AddressSanitizer for executables built to run on the target (i.e. the
    Fuchsia device).  The `/cat` suffix constrains this matching rule only
    to the binary named `cat`.

 3. The third switch is like the second, but matches the binary named `ledger`.
 
 1. 例子中第一项应用了匹配规则`host_asan`, 这条规则使得
    [AddressSanitizer](https://clang.llvm.org/docs/AddressSanitizer.html)
    对所有构建所得的，用于在构建宿主机上运行的可执行文件生效。

 2. 第二项应用了匹配规则`asan`，这条规则使得AddressSanitizer对所有构建所得的，
    用于在某个构建目标上（例如Fuchsia设备）运行的可执行文件生效。
    `/cat`后缀限定这条匹配规则仅适用于名为`cat`的二进制文件。

 3. 第三项和第二项相似，区别在于这一项仅适用于名为`ledger`的二进制文件。


The GN code supports much more flexible matching rules than just the binary
name, but there are no shorthands for those.  To do something more complex,
set the
[`select_variant`](https://fuchsia.googlesource.com/garnet/+/master/docs/gen/build_arguments.md#select_variant)
GN build argument directly.

GN代码支持更加灵活的匹配规则，而不仅仅是匹配二进制文件的名称，但是这种匹配没有对应的速写标记。
如果想要实现这种匹配，需要直接设置GN构建参数
[`select_variant`](https://fuchsia.googlesource.com/garnet/+/master/docs/gen/build_arguments.md#select_variant)。

 * You can do this via the `--args` switch to `gn gen` once you have the
   syntax down.

 * The easiest way to experiment is to start with some `--variant` switches
   that approximate what you want and then edit the `select_variant` value
   `fx set` produces:
   * You can just edit the `args.gn` file in the GN output directory
     (e.g. `out/x64/args.gn`) and the next `ninja` run (aka `fx build`)
     will re-run `gn gen` with those changes.
   * You can use the command `./buildtools/gn args out/x64`, which
     will run your `$EDITOR` on the `args.gn` file and then do `gn gen`
     immediately so you can see any errors in your GN syntax.

 * 如果你熟悉相应的语法规则，那么可以为`gn gen`命令指定`--args`参数以实现上述功能。
 
 * 最简单的实践方式则是使用`--variant`参数指定一个和你所需情况相似的`variant`，然后
   编辑由`fx set`命令所生成的`select_variant`的值。
   * 你可以仅修改GN输出目录中的`args.gn`文件 (例如 `out/x64/args.gn`)。
     下一次`ninja`运行时（也就是`fx build`）将会重新运行`gn gen`，
     以应用之前做出的修改。
   * 你也可以使用命令`./buildtools/gn args out/x64`。这个命令将会使用环境中的`$EDITOR`
     打开`args.gn`文件，然后执行`gn gen`并为你列出你的GN文件中存在的语法错误。

To see the list of variants available and learn more about how to define
new ones, see the
[`known_variants`](https://fuchsia.googlesource.com/garnet/+/master/docs/gen/build_arguments.md#known_variants)
build argument.

如果想要查看可用的variant列表，或者想要了解如何定义新的variant，可以浏览构建参数
[`known_variants`](https://fuchsia.googlesource.com/garnet/+/master/docs/gen/build_arguments.md#known_variants)的文档页面。

# Debugging
#调整

This document is a work-in-progress and provides various suggestions
这个文件应用于程序运行过程中，可以提供许多针对Fuchsia程序运行中出现的错误提出建议。
for debugging Fuchsia programs.

## Backtraces
##堆栈信息

### Automagic backtraces
###自动堆叠

Fuchsia starts a program at boot called "crashlogger" that reports
Fuchsia起始于一个问题关于叫做crashlogger的引导，这个引导展示出问题关于堆栈信息中的崩溃的线程
program crashes and prints a backtrace of the crashing thread.

Example:
举例

```
$ /boot/test/debugger-test segfault
[00029.042] 01093.01133> <== fatal exception: process [6354] thread [6403]
[00029.042] 01093.01133> <== fatal page fault, PC at 0x1001df9
[00029.044] 01093.01133>  CS:                   0 RIP:          0x1001df9 EFL:              0x207 CR2:               0x2a
[00029.044] 01093.01133>  RAX:         0x99999999 RBX:          0x1073e90 RCX:                  0 RDX:               0x2a
[00029.044] 01093.01133>  RSI:          0x1073e90 RDI:                0xa RBP:          0x1073e50 RSP:          0x1073e20
[00029.044] 01093.01133>   R8:          0x1003bba  R9:     0x400000127e00 R10:                  0 R11:          0x1073e90
[00029.044] 01093.01133>  R12:          0x1007030 R13:     0x400000127e40 R14:     0x4000000de5f8 R15:                0xe
[00029.044] 01093.01133>  errc:               0x6
[00029.044] 01093.01133> bottom of user stack:
[00029.045] 01093.01133> 0x01073e20: 99999999 00000000 012bc500 00000000 |..........+.....|
[00029.045] 01093.01133> 0x01073e30: 00000003 00000000 0001a680 00004000 |.............@..|
[00029.045] 01093.01133> 0x01073e40: 012b5008 00000000 00087fda 00004000 |.P+..........@..|
[00029.045] 01093.01133> 0x01073e50: 01073e70 00000000 01001e71 00000000 |p>......q.......|
[00029.045] 01093.01133> 0x01073e60: 01073e90 00000000 01007030 00000000 |.>......0p......|
[00029.045] 01093.01133> 0x01073e70: 01073eb0 00000000 01001eb9 00000000 |.>..............|
[00029.045] 01093.01133> 0x01073e80: 01073ea0 00000000 01001e4c 00000000 |.>......L.......|
[00029.045] 01093.01133> 0x01073e90: 99999999 00004000 0001a4b7 00004000 |.....@.......@..|
[00029.045] 01093.01133> 0x01073ea0: 01073ed0 00000000 01007030 00000000 |.>......0p......|
[00029.045] 01093.01133> 0x01073eb0: 01073ef0 00000000 01001eb9 00000000 |.>..............|
[00029.045] 01093.01133> 0x01073ec0: 01073ee0 00000000 01001e4c 00000000 |.>......L.......|
[00029.045] 01093.01133> 0x01073ed0: 99999999 99999999 00006e68 00000000 |........hn......|
[00029.045] 01093.01133> 0x01073ee0: 01073f10 00000000 01007030 00000000 |.?......0p......|
[00029.045] 01093.01133> 0x01073ef0: 01073f30 00000000 01001eb9 00000000 |0?..............|
[00029.045] 01093.01133> 0x01073f00: 01073f20 00000000 01001e4c 00000000 | ?......L.......|
[00029.045] 01093.01133> 0x01073f10: 99999999 99999999 99999999 00000000 |................|
[00029.045] 01093.01133> arch: x86_64
[00029.064] 01093.01133> dso: id=018157dff2cf7052d9a0e198c08875cf080d1fc5 base=0x4000000e1000 name=<vDSO>
[00029.064] 01093.01133> dso: id=bbcefdf213bbac0271e69abc2ea163e9a7bb83a8 base=0x400000000000 name=libc.so
[00029.064] 01093.01133> dso: id=fcb157b4ae828f8456769dcd217f29745902393c base=0x101a000 name=libfdio.so
[00029.064] 01093.01133> dso: id=62e9ae64cd4b9915f4aa0fae8817df47f236f3ef base=0x1011000 name=liblaunchpad.so
[00029.064] 01093.01133> dso: id=2bdcafce47915aa12e0942d59f44726c3832f0a2 base=0x100d000 name=libunittest.so
[00029.064] 01093.01133> dso: id=cc891abcdb9d2f0d8fcae0ae2a98f421a0a592a8 base=0x1008000 name=libtest-utils.so
[00029.064] 01093.01133> dso: id=48fb9f5b9eff4fbd8b23d1f3db32c1904fabc277 base=0x1000000 name=app:/boot/test/debugger-test
[00029.066] 01093.01133> bt#01: pc 0x1001df9 sp 0x1073e20 (app:/boot/test/debugger-test,0x1df9)
[00029.141] 01093.01133> bt#02: pc 0x1001e71 sp 0x1073e60 (app:/boot/test/debugger-test,0x1e71)
[00029.141] 01093.01133> bt#03: pc 0x1001eb9 sp 0x1073e80 (app:/boot/test/debugger-test,0x1eb9)
[00029.142] 01093.01133> bt#04: pc 0x1001e4c sp 0x1073e90 (app:/boot/test/debugger-test,0x1e4c)
[00029.143] 01093.01133> bt#05: pc 0x1001eb9 sp 0x1073ec0 (app:/boot/test/debugger-test,0x1eb9)
[00029.143] 01093.01133> bt#06: pc 0x1001e4c sp 0x1073ed0 (app:/boot/test/debugger-test,0x1e4c)
[00029.144] 01093.01133> bt#07: pc 0x1001eb9 sp 0x1073f00 (app:/boot/test/debugger-test,0x1eb9)
[00029.144] 01093.01133> bt#08: pc 0x1001e4c sp 0x1073f10 (app:/boot/test/debugger-test,0x1e4c)
[00029.145] 01093.01133> bt#09: pc 0x1001eb9 sp 0x1073f40 (app:/boot/test/debugger-test,0x1eb9)
[00029.145] 01093.01133> bt#10: pc 0x1001e4c sp 0x1073f50 (app:/boot/test/debugger-test,0x1e4c)
[00029.146] 01093.01133> bt#11: pc 0x1001ea3 sp 0x1073f90 (app:/boot/test/debugger-test,0x1ea3)
[00029.146] 01093.01133> bt#12: pc 0x100164a sp 0x1073fb0 (app:/boot/test/debugger-test,0x164a)
[00029.147] 01093.01133> bt#13: pc 0x40000001cce6 sp 0x1073ff0 (libc.so,0x1cce6)
[00029.148] 01093.01133> bt#14: pc 0 sp 0x1074000
[00029.149] 01093.01133> bt#15: end
```

Since debug information is currently not available on the target,
自从调试信息是准确的并不是仅在目标程序才有效。
a program must be run on the development host to translate the raw
一个程序一定要运行在已经发展完成的主机到未经处理的地址。地址在堆栈中以符号的形式存在。
addresses in the backtrace to symbolic form. This program is `symbolize`:
这个程序是`symbolize`：复制上面的程序到一个文件，叫做 `backtrace.out`，并且运行。
Copy the above to a file, say `backtrace.out`, and then run:

```
bash$ ZIRCON_BUILD_DIR=$FUCHSIA_DIR/out/build-zircon/build-x86
bash$ cat backtrace.out | $FUCHSIA_DIR/zircon/scripts/symbolize \
  --build-dir=$ZIRCON_BUILD_DIR \
  $ZIRCON_BUILD_DIR/system/utest/debugger/debugger.elf
[00029.042] 01093.01133> <== fatal exception: process [6354] thread [6403]
... same output as "raw" backtrace ...
start of symbolized stack:
#01: test_segfault_leaf at /home/fuchsia/zircon/system/utest/debugger/debugger.c:570
#02: test_segfault_doit1 at /home/fuchsia/zircon/system/utest/debugger/debugger.c:585
#03: test_segfault_doit2 at /home/fuchsia/zircon/system/utest/debugger/debugger.c:589
#04: test_segfault_doit1 at /home/fuchsia/zircon/system/utest/debugger/debugger.c:582
#05: test_segfault_doit2 at /home/fuchsia/zircon/system/utest/debugger/debugger.c:589
#06: test_segfault_doit1 at /home/fuchsia/zircon/system/utest/debugger/debugger.c:582
#07: test_segfault_doit2 at /home/fuchsia/zircon/system/utest/debugger/debugger.c:589
#08: test_segfault_doit1 at /home/fuchsia/zircon/system/utest/debugger/debugger.c:582
#09: test_segfault_doit2 at /home/fuchsia/zircon/system/utest/debugger/debugger.c:589
#10: test_segfault_doit1 at /home/fuchsia/zircon/system/utest/debugger/debugger.c:582
#11: test_segfault at /home/fuchsia/zircon/system/utest/debugger/debugger.c:599
#12: main at /home/fuchsia/zircon/system/utest/debugger/debugger.c:640
#13: start_main at /home/fuchsia/zircon/third_party/ulib/musl/src/env/__libc_start_main.c:47
#14: (unknown)
end of symbolized stack
```

Any easy way to capture this output from the target is by running
许多简单的方式可以采集到最后的输出通过靶标运行`loglistener`的程序在你已经配置好的主机。
the `loglistener` program on your development host.

### Manually requesting backtraces
###手动请求堆栈

Akin to printf debugging, one can request crashlogger print a
近似指出程序调试错误，一方面可以请求crashlogger印到堆栈中，在你代码中一个详细的点。
backtrace at a particular point in your code.

Include this header:
包含这个报头

```
#include <zircon/crashlogger.h>
```

and then add the following where you want the backtrace printed:
接下来添加下面的程序到你希望你的堆栈在哪里显示。

```
void my_function() {
  ...
  crashlogger_request_backtrace();
  ...
}
```

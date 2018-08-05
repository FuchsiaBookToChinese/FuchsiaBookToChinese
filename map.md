Repository structure
====================
储存库结构
====================

```
//docs
  README.md               # welcome message, table of contents
  自述文件                   欢迎留言，目录
  CODE_OF_CONDUCT.md      # rules and expectations for contributors
  行为守则                   贡献者的规则和期望
  CONTRIBUTING.md         # lay out the ground rules for contributing, redirect
  贡献的                     制定贡献、重定向
                          # development folder
                            开发文件夹
  getting_in_touch.md     # how and why to get in touch with the Fuchsia team
  取得联系                   如何和Fuchsia团队取得联系
  values/                 # various bits about project culture
  价值观念                   关于项目文化的各种信息
  development/            # how to develop in the Fuchsia tree?
  发展                      如何在Fuchsia tree中发展
    README.md             # warn that it’s mainly about development of Fuchsia,
    自述文件                 警告说这主要是关于Fuchsia的发展，
                          # not just targeting Fuchsia
                            不仅仅是针对Fuchsia
    workflows/            # concrete usage patterns for: Jiri & Git, Gerrit, fx,
    工作流程                 具体使用模式为:Jiri & Git, Gerrit, fx,
                          # GN/ninja, etc...
                            GN/ninja, etc...
    best-practices/       # general articles about coding practices
    最佳实践                 关于编码实践的一般文章
    hardware/             # how to use Fuchsia on various devices
    硬件                     如何在各种设备上使用Fuchsia
    languages/            # conventions, tooling for supported languages
    语言                     受支持语言的常规、工具
      style.md            # style guide
      风格                   风格指南
      naming.md           # how to name stuff that’s not covered in the style
      命名                  如何命名样式中未涵盖的内容
                          # guide
                            指南
    sources.md            # explain the version control system: Jiri,
    来源                    解释版本控制系统: Jiri,
                          # fuchsia.googlesource.com, Git, Gerrit
                            fuchsia.googlesource.com, Git, Gerrit
    layers.md             # purpose and nature of the layers, auto-rolling
    层                      层的用途和性质、自动轧制
                          # system, embedded manifests
                            系统，嵌入式清单
    third_party.md        # structure of third-party code, policies, maintenance
    第三方                   第三方代码、策略、维护的结构
    build_system.md       # overview of the build system: GN/Ninja, Zircon
    构建系统                 构建系统概述: GN/Ninja, Zircon
                          # specifics, what the main steps of the build are, how
                            细节，构建的主要步骤是什么，如何
                          # GN targets are structured, build package
                            GN目标是结构化的，构建包
  the-book/               # an academic description of the Fuchsia stack, with
                          # links to implementation
```                         Fuchsia堆栈的学术描述以及实现链接

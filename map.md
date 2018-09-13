Repository structure
====================
储存库结构
====================

```
//docs
  README.md               # 欢迎页，目录                  
  CODE_OF_CONDUCT.md      # 贡献者应遵守的规则和对其的期望              
  CONTRIBUTING.md         # 贡献、重定向的基础规则              
                          # 开发文件夹
  getting_in_touch.md     # 如何以及为何要与 Fuchsia 团队取得联系                  
  values/                 # 关于项目文化的各几个要点
  development/            # 如何在 Fuchsia 结构树中进行开发
    README.md             # 此章主要是关于 Fuchsia 的开发，
                          # 而不仅仅是阐述 Fuchsia
    workflows/            # 具体使用格式指南: Jiri & Git, Gerrit, fx,         
                          # GN/ninja, etc...
    best-practices/       # 关于编码实践的普及性文章       
    hardware/             # 如何在不同的设备上使用 Fuchsia                
    languages/            # 受支持的编程语言的相关规则以及工具                
      style.md            # 风格指南
      naming.md           # 如何命名样式中未涵盖的内容
                          # 指南      
    sources.md            # 阐述版本控制系统: Jiri,      
                          # fuchsia.googlesource.com, Git, Gerrit
    layers.md             # 层的用途和性质、自动滚动系统
                          # 嵌入式设备清单
    third_party.md        # 第三方代码的结构、政策、维护          
    build_system.md       # 构建系统概述: GN/Ninja, Zircon
                          # 细节，构建的主要步骤是什么，
                          # GN 目标是如何结构化的，构建包的步骤
  the-book/               # Fuchsia 堆栈的学术描述以及实现链接
```        
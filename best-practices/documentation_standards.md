# Documentation Standards
# 文档标准

A document about what to document and how to document it for people who create
things that need documentation.

一个关于文档需要描述什么以及如何描述的文档，写给那些为自己所创造的东西而撰写文档的开发者。

## Why document?
## 为什么需要文档？

Fuchsia is a new operating system.  As it grows and new people join the project
so grows the need to provide effective documentation resources.

Fuchsia 是一个全新的操作系统。在它发展的同时，新的项目参与者也不断涌入，提供有效的文档资源的需求也应运而生。

## Who is the audience?
## 谁是读者？

The documentation described here is intended to address a technical audience,
i.e. those who expect to implement or exercise APIs or understand the internal
dynamics of the operating system.  These standards are not intended for
end-user product documentation.

这里所描述的文档旨在为技术人员传达内容，例如，那些想要应用或者练习使用 APIs 或是想要了解操作系统内部动态的技术人员。故这份文档标准并不是写给终端用户产品的。

## What should I document?
## 我需要在文档中描述什么？

In brief, document your interfaces, introduce essential concepts, explain how
everything fits together.

简而言之，描述你的接口，介绍必要的概念，解释一切东西是如何相互契合的。

- Conventions: e.g. this document about documentation, code style
- System Design: e.g. network stack, compositor, kernel, assumptions
- APIs: e.g. FIDL interfaces, library functions, syscalls
- Protocols: e.g. schemas, encodings, wire formats, configuration files
- Tools: e.g. `bootserver`, `netcp`, `fx`
- Workflows: e.g. environment set up, test methodologies, where to find various
  parts, how to get work done

- 公约：例如，对于文档的介绍，代码风格
- 系统设计：例如，网络堆栈，合成器，内核，假设
- APIs：例如，FIDL 接口，库函数，系统调用
- 协议：例如，模式，编码，无线格式，配置文件
- 工具：例如，`bootserver`, `netcp`, `fx`
- 工作流：例如，环境设置，测试方法，哪里可以找到各个部分，如何正确运行

## Where should I put documents?  What goes where?
## 我应该把文档放在哪里？哪个放哪里？

Keep your documentation assets in the source tree near the things they
describe.  Where this should go depends on the type of document and its topic.
(See following sections for details.)

将源文件中的文档资产保存在它们描述的内容附近。放在哪里取决于文档的类型和主题。(详细内容请看下一章节)

### Prose Documentation
### Prose 文档(带程序实例的文档)

Prose documentation is especially effective at explaining big ideas and
demonstrating how to perform particular tasks.  It's great for documenting
system design, protocols, tools, conventions, workflows, and tutorials.

在需要解释一个重要想法以及演示如何执行特定任务时，使用 Prose 文档可以得到很好的效果。通常，这在描述系统设计，协议，工具，公约，工作流，以及教程时都很有效。

Prose documentation should be written in Markdown format and published as a
file with the extension `.md` in the source repository.

Prose 文档应当使用 Markdown 格式撰写，并以 `.md` 后缀发布至源存储库。

Preferred locations:

首选位置：

- Documents about a specific project should go into the `docs` folder at the
  root of that project's repository and be arranged by topic.
  e.g. `//apps/my-project/docs/my-feature.md`
- Documents about Fuchsia as a whole should go into the top-level `docs`
  repository itself.  e.g. `//docs/build_packages.md`

- 关于特定项目的文档应当被放入项目仓库根目录的 `docs` 文件夹，并按照主题命名。
例如 `//apps/my-project/docs/my-feature.md`
- 关于 Fuchsia 整体的文档应当被放入仓库自身顶级的 `docs` 文件夹。
例如 `//docs/build_packages.md`

Alternate locations:

替代位置：

- Adding a `README.md` to the root of a project's repository may serve as a
  brief orientation to the project for first time visitors but this is not
  required.

- 要为首次浏览项目的人做一个简要的项目介绍，你可以在项目仓库的根目录添加一个 `README.md` 文件，但是这不是必须的。

Tips for writing effective prose documentation:

撰写有效的 prose 文档的一些要点：

- Write plain English text.
- Optimize the experience of first time readers.
- Give each document a clear title.
- Briefly describe the purpose and underlying assumptions of each part.
- Be sure to define your jargon; refrain from excess abbreviations.
- Include links to other relevant documentation.
- Stay on topic.
- Use section headers to organize ideas.
- Keep the tone informal.
- Don't restate API documentation which is already published elsewhere (e.g. as
  documentation comments)

- 使用简明的英文文本书写
- 针对首次阅读者优化体验
- 给每个文档取一个明确的标题
- 简要地描述每一部分的目的和潜在假设
- 确保定义你的专业术语，避免过多的缩写
- 为相关的文档创建链接
- 不偏离主题
- 使用小节标题来组织想法
- 保持非正式的语气
- 不要重述已在其它地方发布过的API文档(例如 文档注释)



### Documentation Comments
### 文档注释(Documentation Comments)

Documentation comments are especially effective at describing the purpose of
interfaces, structures, methods, data types, and other elements of program
code.

通过文档注释，可以更加有效地描述接口，接口，方法，数据类型的目的，以及其它程序代码的要素。

Documentation comments should be applied consistently to all public APIs since
they are a valuable asset for SDK consumers.

文档注释应始终如一地应用于所有公共 API，因为这些 API 是SDK 使用者的利器。

Tips for writing effective documentation comments:

撰写有效文档注释的一些要点：

- Write plain English text.
- Write complete sentences and paragraphs.
- Keep comments clear and brief, no more than a few sentences.
- Follow the approved style guide for your programming language.
- Always add value; don't restate what is already indicated by the type
  signature.
- Describe units of measure and integrity constraints of variables.
- Link to prose documentation for more elaborate descriptions of how APIs fit
  together as a whole.

- 使用简明的英文文本书写
- 使用完整的句子和段落
- 保持注释清晰简洁，以几句话结束
- 按照认可的编程语言样式指南进行操作
- 总是增加价值;不要重述类型签名已经指明了的内容
- 描述变量的度量单位和完整性约束
- 链接到 prose 文档，以更详细地描述 API 是如何整合在一起的

### Breadcrumbs
### 面包屑

Documentation is only useful when your audience can find it.  Adding links to
or from existing documentation artifacts greatly improves the chances that
someone will read it.

文档只有能被你的读者找到时才能发挥作用。向现有的或者从现有的文档工件(artifact)添加链接来增大某人阅读到它的机会。

Tips for leaving breadcrumbs:

留下面包屑的一些要点：

- Top-down linkage: Add links from more broadly scoped documents to more
  detailed documents to help readers learn more about particular topics.  The
  [Fuchsia book](../the-book/README.md) is a good starting point for top-down
  linkage.
- Bottom-up linkage: Add links from more detailed documents to more broadly
  scoped documents to help readers develop more awareness of the overall
  context of the topics being discussed.  Adding links from module, class, or
  interface level documentation comments to higher level prose documentation
  overviews can be particularly effective.
- Sideways linkage: Add links to documents in related subject domains with
  which a reader should familiarize themselves in order to better understand
  the content of your document.

- 自上而下的联系：将范围更广的文档中的链接添加到更为详细的文档中，这有助于读者理解有关特定主题的更多信息。
  [Fuchsia book](../the-book/README.md) 是自上而下联动的一个很好的起点。
- 自下而上的联系：将更为详细的文档中的链接添加到范围更广的文档中，这有助于读者加深对正在讨论的主题的整体背景的认识。
  将 moudle，class，以及接口级别的文档注释中的链接添加到更高等级的 prose 文档概述中同样特别有效。
- 侧向的联系：添加链接到相关主题领域的文档，读者应当熟悉这些文档，这有助于他们更好地理解你的文档中的内容。

Contributing Changes
====================

贡献 changse
====================

Fuchsia manages commits through Gerrit at
https://fuchsia-review.googlesource.com. Not all projects accept patches;
please see the CONTRIBUTING.md document in individual projects for
details.

Fuchisia 通过 Gerrit 来管理 commits，详见 https://fuchsia-review.googlesource.com。
并不是所有的项目都支持提交 patch 贡献，详情请查阅各个 项目下的 CONTRIBUTING.md 文档。


## Submitting changes
## 提交 changes

To submit a patch to Fuchsia, you may first need to generate a cookie to
authenticate you to Gerrit. To generate a cookie, log into Gerrit and click
the "Generate Password" link at the top of https://fuchsia.googlesource.com.
Then, copy the generated text and execute it in a terminal.

Once authenticated, follow these steps to submit a patch to a repo in Fuchsia:

要提交 patch 到 Fuchsia 项目，首先，你需要生成一个 cookie 来验证你的 Gerrit 账户。要生成
cookie，需要登陆 Gerrit，并点击 https://fuchsia.googlesource.com 链接页面顶端的 "Generate Password"。
之后，拷贝生成的文本，粘贴至控制台来执行。

验证之后，按照以下的步骤来提交 patch 到 Fuchsia 的仓库。

```
# create a new branch
git checkout -b branch_name

# write some awesome stuff, commit to branch_name
# edit some_file ...
git add some_file
# if specified in the repo, follow the commit message format
git commit ...

# upload the patch to Gerrit
# `jiri help upload` lists flags for various features, e.g. adding reviewers
jiri upload # Adds default topic - ${USER}-branch_name
# or
jiri upload -topic="custom_topic"
# or
git push origin HEAD:refs/for/master

# at any time, if you'd like to make changes to your patch, use --amend
git commit --amend

# once the change is landed, clean up the branch
git branch -d branch_name
```

See the Gerrit documentation for more detail:
[https://gerrit-documentation.storage.googleapis.com/Documentation/2.12.3/intro-user.html#upload-change](https://gerrit-documentation.storage.googleapis.com/Documentation/2.12.3/intro-user.html#upload-change)

### Commit message tags
### Commit 信息标签

If submitting a change to Zircon, Garnet, Peridot or Topaz, include [tags] in
the commit subject flagging which module, library, app, etc, is affected by the
change. The style here is somewhat informal. Look at these example changes to
get a feel for how these are used.

如果要提交一个 change 到 Zircon, Garnet, Peridot 或者 Topaz，请在提交主题中包含 [tags] 用来标记涉及了哪个
模块，库，应用等。这其中的风格有些不正式，查看下面的例子来掌握一种如何使用的感觉。

* https://fuchsia-review.googlesource.com/c/zircon/+/112976
* https://fuchsia-review.googlesource.com/c/garnet/+/110795
* https://fuchsia-review.googlesource.com/c/peridot/+/113955
* https://fuchsia-review.googlesource.com/c/topaz/+/114013

Gerrit will flag your change with
`Needs Label: Commit-Message-has-tags` if these are missing.

如果你的 change 缺失了，Gerrit 将为你的 change 标记上 `Needs Label: Commit-Message-has-tags`

例如:

Example:
```
# Ready to submit
[parent][component] Update component in Topaz.
Test: Added test X

# Needs Label: Commit-Message-has-tags
Update component in Topaz.
Test: Added test X
```

### Commit message "Test:" labels
### Commit 信息 "Test:" 标签

Changes to Zircon, Garnet, Peridot, and Topaz require a "Test:" line in the
commit message.

We normally expect all changes that modify behavior to include a test that
demonstrates (some aspect of) the behavior change. The test label should name
the test that was added or modified by the change:

当你提交 change 至 Zircon, Garnet, Peridot 和 Topaz 时，你需要附加一行 "Test:" 到你的 commit 信息里。

通常，我们希望所有存在行为更改的 change 都能包含一个(在某些方面)演示这些更改了的行为的 test。
test 标签应该应该命名那些在 change 中添加或是修改了的 test。

```
Test: SandboxMetadata.ParseRapidJson
```

Some behavior changes are not appropriate to test in an automated fashion. In
those cases, the test label should describe the manual testing performed by the
author:

某些行为的更改不适合以自动方式进行测试。在那些情况下，作者 应当在 test 标签种描述自己手动测试的结果:

```
Test: Manually tested that the keyboard still worked after unplugging and
      replugging the USB connector.
```

In some cases, we are not able to test certain behavior changes because we lack
some particular piece of infrastructure. In that case, we should have an issue
in the tracker about creating that infrastructure and the test label should
mention the bug number in addition to describing how the change was manually
tested:

在某些情况下，我们不能测试某些行为的更改，因为我们缺少一些特定的基础架构。在这样的情况下，我们
应该在跟踪其中附上关于如何创建基础架构的 issue，除了描述 change 如何手动测试之外，test 标签中
还应当提及错误号。


```
Test: Manually tested that [...]. Automated testing needs US-XXXX
```

If the change does not change behavior, the test line should indicate that you
did not intend to change any behavior:

如果 change 没有行为变更，test 行应当表面你不打算更改任何的行为:

```
Test: No behavior change
```

If there's a test suite that validates that your change did not change behavior,
you can mention that test suite as well:

如何有一个测试套件可以验证你的 change 没有更改行为，你也可以附上这个测试套件:

```
Test: blobfs-test
```

Alternatively, if the change involves updating a dependency for which the commit
queue should provide appropriate acceptance testing, the test label should defer
to the commit queue:

此外，针对需要提供适当验收测试的 commit 序列，change 如果涉及到需要更新其依赖，则 test 标签应当遵循
commit 队列:

```
Test: CQ
```

Syntactically, commit messages must contain one of {test, tests, tested, testing}
followed by ':' or '='. Any case (e.g., "TEST" or "Test") works.

从语法上来讲，commit 信息必须包含 {test, tests, tested, testing} 中的其中一个，并跟随
':' 或者 '='。任何情况(例如  "TEST" 或 "Test")都有效。

All of these are valid:
以下的这些都是有效的:

```
TEST=msg

Test:msg

Testing : msg

  Tested = msg

Tests:
- test a
- test b
```

(See https://fuchsia.googlesource.com/All-Projects/+/refs/meta/config/rules.pl
for the exact regex.)

(正确的正则表达式请查阅 
https://fuchsia.googlesource.com/All-Projects/+/refs/meta/config/rules.pl)

Gerrit will flag your change with `Needs Label: Commit-Message-has-TEST-line` if
these are missing.

如果你的 change 缺失了，Gerrit 将为你的 change 标注上 `Needs Label: Commit-Message-has-TEST-line`。

Example:

例如:

```
# Ready to submit
[parent][component] Update component in Topaz.
Test: Added test X

# Needs Label: Commit-Message-has-TEST-line
[parent][component] Update component in Topaz.
```

## [Non-Googlers only] Sign the Google CLA
## [非 Googleers] 签署 Google CLA

In order to land your change, you need to sign the [Google CLA](https://cla.developers.google.com/).
为了使你的 change 生效，你需要签署 [Google CLA](https://cla.developers.google.com/).

## [Googlers only] Issue actions
## [Googlers] Issue 行为

Commit messages may reference issue IDs in Fuchsia's
[issue tracker](https://fuchsia.atlassian.net/); such references will become
links in the Gerrit UI. Issue actions may also be specified, for example to
automatically close an issue when a commit is landed:

Commit 消息可能会引用 Fuchsia [issue tracker](https://fuchsia.atlassian.net/)中的
issue IDS；类似这样的引用将以链接的形式在 Gerrit UI 中展现。Issue 行为也可以被指定为
某种具体操作，例如，当 commit 生效时自动关闭 issue:


BUG-123 #done

`done` is the most common issue action, though any workflow action can be
indicated in this way.

`done` 是最常常见的 issue 行为，但可以通过这种方式指示任何的 workflow 行为。

Issue actions take place when the relevant commit becomes visible in a Gerrit
branch, with the exception that commits under refs/changes/ are ignored.
Usually, this means the action will happen when the commit is merged to
master, but note that it will also happen if a change is uploaded to a private
branch.

除了 refs/changes/ 下的 commit 会被忽略，当其它 commit 在 Gerrit branch 中变得
可见时，Issue 行为将会被触发。
通常，这意味当 commit merge 到 master  分支时，action 将会触发。但需要注意的是，当
change 被提交到一个私有分支时，action 同样会触发。

*Note*: Fuchsia's issue tracker is not open to external contributors at this
time.

*注意*：Fuchsia 的 issue 跟踪器目前不向外部贡献者开放。

## Cross-repo changes
## 跨仓库 changes

Changes in two or more separate repos will be automatically tracked for you by
Gerrit if you use the same topic.

当你的 changes 在两个或两个以上独立仓库中使用了同样的主题，将会由 Gerrit 为你自动追踪
这些 changes。

### Using jiri upload
### 使用 jiri 上传

Create branch with same name on all repos and upload the changes

以同样的名称在所有的仓库中创建分支，并提交 changes

```
# make and commit the first change
cd fuchsia/bin/fortune
git checkout -b add_feature_foo
* edit foo_related_files ... *
git add foo_related_files ...
git commit ...

# make and commit the second change in another repository
cd fuchsia/build
git checkout -b add_feature_foo
* edit more_foo_related_files ... *
git add more_foo_related_files ...
git commit ...

# Upload all changes with the same branch name across repos
jiri upload -multipart # Adds default topic - ${USER}-branch_name
# or
jiri upload -multipart -topic="custom_topic"

# after the changes are reviewed, approved and submitted, clean up the local branch
cd fuchsia/bin/fortune
git branch -d add_feature_foo

cd fuchsia/build
git branch -d add_feature_foo
```

### Using Gerrit commands
### 使用 Gerrit 命令

```
# make and commit the first change, upload it with topic 'add_feature_foo'
cd fuchsia/bin/fortune
git checkout -b add_feature_foo
* edit foo_related_files ... *
git add foo_related_files ...
git commit ...
git push origin HEAD:refs/for/master%topic=add_feature_foo

# make and commit the second change in another repository
cd fuchsia/build
git checkout -b add_feature_foo
* edit more_foo_related_files ... *
git add more_foo_related_files ...
git commit ...
git push origin HEAD:refs/for/master%topic=add_feature_foo

# after the changes are reviewed, approved and submitted, clean up the local branch
cd fuchsia/bin/fortune
git branch -d add_feature_foo

cd fuchsia/build
git branch -d add_feature_foo
```

Multipart changes are tracked in Gerrit via topics, will be tested together,
and can be landed in Gerrit at the same time with `Submit Whole Topic`. Topics
can be edited via the web UI.

## Changes that span repositories
## 跨仓库的 changes

See [Changes that span repositories](development/workflows/multilayer_changes.md).

详见 [跨仓库的 changes](development/workflows/multilayer_changes.md)

## Resolving merge conflicts
## 解决 merge 冲突

```
# rebase from origin/master, revealing the merge conflict
git rebase origin/master

# resolve the conflicts and complete the rebase
* edit files_with_conflicts ... *
git add files_with_resolved_conflicts ...
git rebase --continue
jiri upload

# continue as usual
git commit --amend
jiri upload
```

## Github integration
## Github 集成

While Fuchsia's code is hosted at https://fuchsia.googlesource.com, it is also
mirrored to https://github.com/fuchsia-mirror. To ensure Fuchsia contributions
are associated with your Github account:

虽然 Fuchsia 的代码托管在 https://fuchsia.googlesource.com，但在  https://github.com/fuchsia-mirror
中也有一个镜像。为确保你的 Github 账户关联了对 Fuchsia 的贡献：

1. [Set your email in Git](https://help.github.com/articles/setting-your-email-in-git/).
2. [Adding your email address to your GitHub account](https://help.github.com/articles/adding-an-email-address-to-your-github-account/).
3. Star the project for your contributions to show up in your profile's
Contribution Activity.

1. [在 Git 中设置你的 email](https://help.github.com/articles/setting-your-email-in-git/).
2. [在你的 Github 账户中加入你的 Email 地址](https://help.github.com/articles/adding-an-email-address-to-your-github-account/).
3. 为您的贡献项目加注星标，以显示在你个人资料的贡献活动中。
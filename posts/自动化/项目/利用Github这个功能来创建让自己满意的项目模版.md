# 快来利用 Github 这个功能来创建让自己满意的项目模版吧

我承认我实在想不出更好的标题了，因为本文涉及到的内容比较宽泛。但我保证，本文会尽可能做到抛砖引玉的效果。不仅仅是创建项目模版，写项目的时候需要考虑的东西本文也适合。

本文主要会讲以下几个内容：

1. `Github` 的模版仓库功能
1. 开源项目（模版）要考虑的 9 个工程化要素
1. 一些创建项目（模版）时提升效率的技巧

阅读过程中可以同时参考[我写的一个模版仓库样例](https://github.com/vabaly/typescript-command-line-tool-template)，如有常用 `TypeScript` 和 `VSCode` 的读者，食用起来会更佳。

## 目录

- [背景](#background)
- [如何做到满意](#how)
    - [TypeScript](#typescript)
        - [Tips](#typescript-tips)
    - [编码规范](#code)
        - [Tips](#code-tips)
    - [测试框架](#test)
        - [Tips](#test-tips)
    - [Commit 规范](#commit)
    - [CI / CD](#ci-cd)
    - [文档](#docs)
    - [CHANGELOG](#changelog)
    - [协议](#licenses)
    - [`.github` 目录](#github)
- [基于模版仓库创建新的项目](#create)
    - [设置仓库为模版仓库](#template)
    - [使用模版仓库](#using)
    - [后期工作](#after)
- [交流](#talk)

## <a name="background" href="#background">背景</a>

开源社区有很多工具可以帮助开发者快速生成一个**个人项目**，但有时候你可能会遇到以下问题：

1. 工具生成的项目过于复杂，很多东西都没必要
1. 生成过程中设置了很多问题，每次都回答一遍很繁琐
1. 生成项目后仍需要修修改改，加上自己喜欢的一些内容
1. 找不到完全适用于自己的模版

或许你已经从零开始或基于类似 `Yeoman` 的工具开发了一个自己的脚手架，但其实 `Github` [创建模板仓库](https://help.github.com/cn/articles/creating-a-template-repository)的功能就可以满足你的需求。

这个功能适合满足以下条件的项目模版：

**项目模版完全定制化，而非通用化**。这意味着无法像 CLI 工具一样通过设置问题来替换模版中相应的内容，从而做到不同喜好的人也能通过选择来生成自己想要的项目。

**所以，如果你经常创建个人项目，并且每次都和前几次架构差不多，那么通过这种方法来创建自己的模版是再简单不过了。**

## <a name="how" href="#how">如何做到满意</a>

在讲 `Github` 的模版仓库功能之前，我们先来谈一谈创建一个模版仓库需要考虑哪些问题，才能使得这个仓库让自己满意。毕竟你需要从零开始创建一个仓库，才能在 `Github` 上将它设为模版仓库。

一般来说，开发者总是会对自己的项目感到满意，即便以后学了新的知识发现原来很糟糕。这是因为创建项目时的所作所为已经达到了自己的现有水平，从而获得了满足感。

所以，我们在建立项目的时候，应该通过对比当时一些知名的开源项目来建立满意度，而不是基于自己现有水平来建立。它们的高度更高，产生满意的情绪更难。但是一旦满意，那么你的项目高度也就上去了。

**接下来，我会列举 9 个开源项目要考虑的工程化要素，并且给出一些创建项目模版时快速达到该工程化目标的技巧，希望能给大家一些启示。**

### <a name="typescript" href="#typescript">TypeScript</a>

如果你准备创建的项目满足以下任一条件，那么你就该考虑使用 `TypeScript` 来编写你的项目了：

1. 从时间上看，是有可能中长期更新或者维护的项目
1. 从空间上看，项目不是很小，有一定复杂度
1. 从情感上看，想要引入类型系统让自己舒心，或者喜欢写类型和接口

#### <a name="typescript-tips" href="#typescript-tips">Tips</a>

1. 本地安装 `typescript` 后，可以使用 `npx tsc --init` 来生成一份 `tsconfig.json` 文件。该文件列有 `TypeScript` 所有的配置，你只需要取消相应配置的注释就可以启用它了。**这个配置最好放在项目模版里，省得每次都要复制粘贴。**

1. 如果你的项目模版是 `Node` 相关的，别忘了在 `package.json` 中加入 `@types/node`。

### <a name="code" href="#code">编码规范</a>

一个项目需要有统一的编程风格和规范，一般来说，使用 `EditorConfig` 来规范编辑器的格式，使用 `ESLint` 来检查代码错误和规范编程风格。市面上最流行的 `ESLint` 规范应该是 [Airbnb JavaScript Style Guide](https://github.com/airbnb/javascript) 和 [JavaScript Standard Style](https://github.com/standard/standard) 这两个了。

#### <a name="code-tips" href="#code-tips">Tips</a>

1. 如果你使用 `VSCode`，可以安装一个 `EditorConfigGenerator` 插件来快速生成一份 `.editorconfig` 文件。
1. 本地安装好 `eslint` 之后，可以使用 `npx eslint --init` 来快速生成一份自己想要的 `.eslintrc.js` 的配置。
1. 如果你使用 `TypeScript` 语言和 `VSCode` 来开发，你或许还需要在项目模版根目录下新建一个 `.vscode` 目录，并且在该目录下新建一个 `settings.json` 文件，写下以下内容，以便启用 `VSCode` 中 `ESLint` 插件的自动修复功能：

    ```jsonc
    {
        "eslint.validate": [
            "javascript",
            "javascriptreact",
            {
                "language": "typescript",
                // 这个属性就代表要对 typescript 语言启用自动修复功能
                "autoFix": true
            }
        ]
    }
    ```

    配置好后，便可以在 `VSCode` 的命令面板中找到 `ESLint: Fix all auto-fixable Problems` 进行自动修复了：

    ![eslint auto fix](https://raw.githubusercontent.com/vabaly/picture/master/images20191011-eslint-fix.png)

### <a name="test" href="#test">测试框架</a>

一个良好的开源项目一定是要有比较高的测试覆盖率的，所以选择一个测试框架来写测试代码至关重要。我比较喜欢 [Jest](https://jestjs.io/)，因为上手容易，一个包就能解决很多诉求，同时有大厂背书，比较适合个人开源项目。

> 关于 Jest 的一些核心配置可以阅读[这篇文章](https://github.com/vabaly/blog/issues/1)

#### <a name="test-tips" href="#test-tips">Tips</a>

* 本地安装好 `jest` 之后，可以使用 `npx jest --init` 来快速生成一份 `jest.config.js` 配置文件。

### <a name="commit" href="#commit">Commit 规范</a>

优秀的开源项目往往都有规范、容易阅读的 Commit 信息，我们可以借助一些工具来帮助我们达到目的。主要是从以下两点入手：

1. 在写 Commit 信息时提醒如何填写关键信息，例如提交类型、涉及更改的文件等；

    为了实现这一点，我们可以借助 `husky` 来指定 `prepare-commit-msg` 钩子触发时执行的命令，也就是在 `git commit` 运行后立马执行的命令。该命令实际执行的程序可以使用 `commitizen`。在 `package.json` 中这样写：

    ```jsonc
    {
        // 其他配置......
        "husky": {
            "hooks": {
                "prepare-commit-msg": "exec < /dev/tty && git cz --hook || true"
            }
        }
        // 其他配置......
    }
    ```

    `commitizen` 就是一款提示你如何填写 Commit 的命令行工具，可通过 `git cz` 直接调用。它本身不包含 Commit 规范，需要另外安装一个规范库来搭配使用。比较流行的规范是 Angular 团队的 Commit 规范，对应的包是 `cz-conventional-changelog`。你需要在 `package.json` 中写下如下配置才能让 `commitizen` 使用这个规范：

    ```jsonc
    {
        // 其他配置......
        "config": {
            "commitizen": {
                "path": "./node_modules/cz-conventional-changelog"
            }
        }
        // 其他配置......
    }
    ```

1. 提交 Commit 信息时验证其是否符合 Commit 规范。

    要实现这一点，我们同样要借助 `husky`，在 `commit-msg` 钩子这执行检验 Commit 的命令。负责这一命令的工具是 `@commitlint/cli`，它也不包含校验的规则，需要另外安装 `@commitlint/config-conventional` 来让 `commitlint` 明确是按 Angular 团队的规范来校验 Commit 信息。在 `pakcage.json` 这样写：

    ```jsonc
    {
        // 其他配置......
        "husky": {
            "hooks": {
                "prepare-commit-msg": "exec < /dev/tty && git cz --hook || true",
                // husky 新增的配置，运行 commitlint 命令
                "commit-msg": "commitlint -E HUSKY_GIT_PARAMS"
            }
        },
        // commitlint 配置
        "commitlint": {
            "extends": [
                // commitlint 校验的规范标准
                "@commitlint/config-conventional"
            ]
        }
        // 其他配置......
    }
    ```

上述两个内容配置好后，当你运行 `git commit` 的时候，会产生以下的效果：

![Commit 运行效果](https://raw.githubusercontent.com/vabaly/picture/master/render1570712509623.gif)

### <a name="ci-cd" href="#ci-cd">CI / CD</a>

优秀的开源项目都会有自动化测试，而在提交代码的时候必须通过测试才能合到主分支，这个测试过程就可以放到 CI 上来进行。另外，如果你的项目还涉及到构建、部署等，那就更需要 CI / CD 了。

对于在 `Github` 上开源的项目来说，`Github Actions` 就是一个非常不错的选择。

`Github Actions` 首次使用需要用户去它的[官网](https://github.com/features/actions)申请，申请好了以后，就会发现自己的所有仓库中都会有个 `Actions` 的选项卡，这里面就能看到该仓库 CI / CD 的情况。

所以，在你的项目或项目模版里，可以新建一个 `.github` 目录，并且在该目录下新建一个 `workflows` 目录，然后在这个目录下随便建一个 `.yml` 文件，一般叫 `build.yml`，因为在 `README` 的徽章里会使用这个名字，如果 CI / CD 通过的话，则这个徽章会显示绿色，并且文字是 `build passing`。

![build passing](https://raw.githubusercontent.com/vabaly/picture/master/imagesbuild-passing.png)

例如，如果你的项目需要跑一下测试，`build.yml` 里面就能这么写：

```yml
# 持续集成工作流
# 语法：https://help.github.com/cn/articles/workflow-syntax-for-github-actions

name: build

on: push

# 任务
jobs:
    # 测试任务
    test:
        name: test
        # 运行环境
        runs-on: ${{ matrix.os }}

        # 构建矩阵，让任务在以下平台和 Node 版本中都跑一遍
        strategy:
            matrix:
                os: [ubuntu-latest, windows-latest, macOS-latest]
                node: [6, 8, 10]

        # 运行步骤
        steps:
        # actions/checkout 为必须的，用于拉取代码到虚拟环境中
        - uses: actions/checkout@v1
        # actions/setup-node 非必须的，这里使用只是为了后续可以使用各种版本的 node
        - uses: actions/setup-node@v1
          with:
            node-version: ${{ matrix.node }}
        - run: |
            npm i
            npm run test
```

想要了解更多 `Github Actions` 的内容，可以直接查看[官方的中文文档](https://help.github.com/cn/articles/configuring-a-workflow)，写的挺详细的。

### <a name="docs" href="#docs">文档</a>

良好的 `README` 文件能够使你的项目更容易理解和使用，多语言的 `README` 更会使你的项目大放异彩。一般 `README.md` 文件是英文文档，`README.zh-CN.md` 是中文文档。**强烈建议新手按照 [Standard Readme Style](https://github.com/RichardLitt/standard-readme) 规范来编写自己的文档**，多写几遍，你就明白 `README` 该写些什么东西了。

### <a name="changelog" href="#changelog">CHANGELOG</a>

几乎每一个开源项目都会写 `CHANGELOG`，它和 `README` 一样重要。请注意，`CHANGELOG` 是写给未来的自己或参与项目的其他人看的，更是写给用户看的。所以这里就不推荐自动把 `Commit` 拼凑成 `CHANGELOG` 的工具了，除非你的 `Commit` 信息写的足够人性化，那么你倒是可以用一用 [conventional-changelog](https://github.com/conventional-changelog/conventional-changelog)。

我更建议新手手动写 `CHANGELOG`，和 `README` 一样，`CHANGELOG` 在社区也有一套 [keep a changelog](https://keepachangelog.com/zh-CN/1.0.0/) 规范，它会教你如何把 `CHANGELOG` 写好。

### <a name="licenses" href="#licenses">协议</a>

养成书写协议的习惯，从而对多数开源项目的版权有一定了解，也对自己未来的开源项目需要使用哪些协议有清晰的认识。一般来说都是采用 `MIT` 协议，这是最为宽松的协议。**如果你还不知道该使用什么协议，[那么这个网站可以帮到你](http://choosealicense.online/)。**

在你项目模版的 `README` 末尾，写上协议名称和仓库作者。另外再创建一个 `LICENSE` 文件，写明协议的详细信息。

### <a name="github" href="#github">`.github` 目录</a>

如果你的项目是与他人合作或者希望其他人来帮助改进，那么这些帮助在 `Github` 上协作的文档就可以放在 `.github` 目录下。常见的会放入以下文件：

1. `COMMIT_CONVENTION.md`：该文档是 `Commit` 规范的内容，帮助其他开发者知道项目需要如何 `Commit`
1. `CONTRIBUTING.md`：该文档是告诉其他开发者要怎么样对该项目进行贡献的
1. `ISSUE_TEMPLATE.md`：该文档是告诉提 `issue` 的用户如何写 `issue` 并提供 `issue` 模版
1. `PULL_REQUEST_TEMPLATE.md`：该文档是告诉提 `PR` 的用户如何写 `PR` 并提供 `PR` 模版

有了这些文件，会使你的项目更加友好。

## <a name="create" href="#create">基于模版仓库创建新的项目</a>

现在你已经知道了创建一个项目或项目模版大概要考虑哪些工程化因素了，当然，工程化还远远不止这些，不同类型的项目所需要的内容也不一样。上述内容也仅刚刚好能满足我开发一个[命令行工具模版仓库](https://github.com/vabaly/typescript-command-line-tool-template)。不过，我已经可以基于这个初级模版仓库创建新的项目了。

### <a name="template" href="#template">设置仓库为模版仓库</a>

1. 将创建好的仓库上传到 `Github`（切记不要把模版仓库的 `package-lock.json` 上传上去，但同时 `.gitignore` 不能忽略它，因为新项目需要上传上去）
1. 在 `Github` 仓库的 `Settings` 选项卡中勾选中 `Template repository` 一项

    ![template repository](https://raw.githubusercontent.com/vabaly/picture/master/20191011_template_choose.png)

这样你的仓库就变成一个模版仓库了。

### <a name="using" href="#using">使用模版仓库</a>

仓库被设置成模版仓库后，你就能在仓库的主页找到 `Use this template` 按钮：

![use this template](https://raw.githubusercontent.com/vabaly/picture/master/20191011-use-this-template.png)

点击此按钮就可以基于该模版仓库创建一个新的仓库了，试试看吧。

### <a name="after" href="#after">后期工作</a>

新仓库被创建后，克隆下来，会有几个地方需要修改，可以在设计模版仓库时使用一些技巧并借助编辑器批量完成：

1. 新仓库的名称批量修改。设计模版仓库时凡是要修改成项目名称的地方都写上模版仓库的名称，然后在新项目中搜索它并全部替换成新仓库的名称。
1. `package.json` 的 `description` 要修改成对新仓库的描述。

## <a name="talk" href="#talk">交流</a>

大家有兴趣可以[关注一下我的博客](https://github.com/vabaly/blog)，也可以关注我的公众号「前端拾贝」，以获取更多原创好文，并在讨论区进行交流。这些文章都是来自于**日常开发中的总结**，对**理论和实践**都比较有帮助：：

![微信公众号二维码](https://raw.githubusercontent.com/vabaly/picture/master/imageswechat-qrcode.jpg)

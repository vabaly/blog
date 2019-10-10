# 利用 Github 这个功能来创建让自己满意且个性化的项目模版

开源社区有很多工具可以帮助我们快速生成一个项目，但有时候你会发现这些模版过于复杂，导致自己无法掌控全局，或者生成项目后仍需要修修改改，加上一些自己常用的一些内容，又或者根本找不到完全适用于自己的模版。当然，你可以开发一款自己的项目生成工具，或者弄一个 Github 仓库克隆下来改一改。但其实 Github 早已提供了一个更方便的方案，只是可能不讨太人注意，那就是[创建模板仓库](https://help.github.com/cn/articles/creating-a-template-repository)。

这种方案适合满足以下条件的项目模版：

**项目模版完全个性化，而非通用化**。这意味着无法像 CLI 工具一样通过设置问题来替换模版中相应的内容，从而达到不同喜好的人也能通过选择来生成自己想要的项目的效果。

所以，如果你经常创建个人项目，并且每次都和前几次架构都差不多，比如总是创建命令行工具类项目，测试框架总是使用 Jest，ESLint 规则总是一样，开发语言总是 Typescript 等等，那么通过这种方案来创建自己的模版是再简单不过的了。

## 如何做到满意呢

一般来说，开发者总是会对自己的项目感到满意，即便以后学到新的知识后发现原来很糟糕，因为井底之蛙是不知道自己在井底的。所以，我们在建立项目的时候，应该和现在知名的一些开源项目进行对比，通过对比它们建立满意度而不是对比自己现有水平来建立，它们的高度更高，产生满意的情绪更难。

这里，我把我现在创建命令行工具的项目模版要考虑的因素以及一些提高创建项目模版效率的技巧分享给大家，希望能给大家一些帮助。

### 编码规范

一个项目需要有统一的编程风格和规范，一般来说，使用 `EditorConfig` 来规范编辑器的格式，使用 `ESLint` 来检查代码错误和规范编程风格。

#### Tips

1. 如果你使用 `VSCode`，可以安装一个 `EditorConfigGenerator` 插件来快速生成一份 `.editorconfig` 文件。
1. 本地安装好 `eslint` 之后，可以使用 `npx eslint --init` 来快速生成一份自己想要的 `.eslintrc.js` 的配置。

### 测试框架

一个良好的开源项目一定是要有比较高的测试覆盖率的，所以选择一个测试框架来写测试代码至关重要。我比较喜欢 `Jest`，因为上手容易，一个包就能解决很多诉求，同时有大厂背书，比较适合个人开源项目。

#### Tips

* 本地安装好 `jest` 之后，可以使用 `npx jest --init` 来快速生成一份 `jest.config.js` 配置文件。

### Commit 规范

优秀的开源项目往往都有规范、容易阅读的 Commit 信息，我们可以借助一些工具来帮助我们规范提交。可以从以下两点入手：

1. 在写 Commit 信息时提醒如何填写关键信息，例如提交类型、涉及更改的文件等；

    为了实现这一点，我们可以借助 `husky` 来指定 `prepare-commit-msg` 钩子触发时执行的脚本，也就是在 `git commit` 运行后立马执行的脚本。该脚本实际执行的程序可以使用 `commitizen`，我们可以在 `package.json` 中这样写：

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

    `commitizen` 就是一款提示你如何填写 Commit 的命令行工具，可通过 `git cz` 直接调用。它本身不包含 Commit 规范，需要另外再安装一个规范库来搭配使用，比较流行的规范是 Angular 团队的 Commit 规范，对应的包是 `cz-conventional-changelog`，你需要在 `package.json` 中写下如下配置才能让 `commitizen` 使用这个规范：

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

    另外，别忘记在 `package.json` 的 `devDependencies` 中加入上述包。

1. 提交 Commit 信息时验证其是否符合 Commit 规范。

    要实现这一点，我们同样要借助 `husky`，在 `commit-msg` 钩子这执行检验 Commit 的命令，负责这一命令的工具是 `@commitlint/cli`，它也不包含校验的规则，需要另外安装 `@commitlint/config-conventional` 来让 `commitlint` 明确是按 Angular 团队的规范来校验 Commit 信息。你需要在 `pakcage.json` 中写下如下的配置：

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

    同样别忘记在 `package.json` 中加入上述包。

上述两个内容配置好后，你可以直接在项目模版中随便改个东西，`git add` 它，再运行 `git commit`，会产生以下的效果：

![Commit 运行效果](https://raw.githubusercontent.com/vabaly/picture/master/render1570712509623.gif)

### 文档

良好的 `README` 文件能够使你的项目更容易理解和使用，多语言的 `README` 更会使你的项目大放异彩。一般 `README.md` 文件是英文文档，`README.zh-CN.md` 是中文文档。**强烈建议新手按照 [Standard Readme Style](https://github.com/RichardLitt/standard-readme) 规范来编写自己的文档**，多写几遍，你就明白 `README` 该写些什么东西了。

### 协议

养成书写协议的习惯，从而对多数开源项目的版权有一定了解，也对自己未来的开源项目需要使用哪些协议有清晰的认识。一般来说都是采用 `MIT` 协议，这是最为宽松的协议。**如果你还不知道该是使用什么协议，[这个网站可以帮到你](http://choosealicense.online/)。**

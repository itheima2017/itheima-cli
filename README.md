# IT 黑马脚手架工具

![黑马程序员](http://or45inefq.bkt.clouddn.com/logo-heima150.png)

## 设计目标

* 提供一套通用的模式复用程序模板
* 规范程序模板的抽取
* 尽可能的自动化处理简化操作

## 感谢

本项目参考了以下开源项目，在详细阅读了开源代码的基础上，在考虑普遍适用性为基础进行开发。

* [vue脚手架](https://github.com/vuejs/vue-cli)
* [蚂蚁金服dva](https://github.com/dvajs/dva)
* [create-react-app](https://github.com/facebook/create-react-app)

## 安装

环境: [Node.js](https://nodejs.org/en/) (>=6.x, 8.x 推荐), npm version 3+ and [Git](https://git-scm.com/).

```bash
npm install -g itheima-cli
```

> 推荐 `-g` 全局安装
>
> macos 需要 `sudo`

## 使用

* 命令格式

```bash
itheima init <template-name> <project-name>
```

* 示例

```bash
itheima init itheimaAdmin-template my-admin
```

> `itheimaAdmin-template` 是 `传智研究院` 提供的 `后台管理模板`

* 参数选项

| 选项        | 说明             |
| ----------- | ---------------- |
| -c, --clone | 克隆 git         |
| --offline   | 使用本地缓存模板 |

## 官方模板

| 模板                                                                          | 说明                     |
| ----------------------------------------------------------------------------- | ------------------------ |
| [itheimaAdmin-template](https://github.com/itheima2017/itheimaAdmin-template) | `黑马Admin` 商用基础模板 |

## 运行演示

[![asciicast](http://or45inefq.bkt.clouddn.com/itheima-cli.gif)](https://asciinema.org/a/nOBpgtncyuPtGCkguvIuKxoYx)

## 自定义模板

你可以自己动手编写模板来满足需要。

按这样运行：

```bash
itheima init username/repo my-project
```

* 模板格式

git服务商 | 说明
---------|-------------
github      | username/repo
bitbucket   | bitbucket:username/repo
branches    | username/repo#branch

> 如果您想从一个私有存储库下载使用 `--clone` 标志， cli 将使用 `SSH keys` `git clone`。

## 使用本地模板

您还可以在本地文件系统上使用模板，而不是GitHub repo。

```bash
itheima init ~/fs/path/to-custom-template my-project
```

## 从头开始编写自定义模板

* 模板repo **必须** 有一个保存模板文件的 `template` 目录。

* 模板repo **可以** 有模板的元数据文件，文件是 `meta.js` 。 它可以包含以下字段：

   * `prompts`：用于收集用户选项数据;

   * `filters`：用于条件筛选文件进行渲染。

   * `metalsmith`：用于在链中添加自定义metalsmith插件。

   * `completeMessage`：生成模板时显示给用户的消息。 你可以在这里包含自定义指令。

   * `complete`：不用 `completeMessage`，你可以使用一个函数来运行生成模板时的东西。

> 强烈推荐大家可以参考官方的模板，作为你的代码模板
>
> 有问题欢迎大家 [issues](https://github.com/itheima2017/itheima-cli/issues)

### prompts

元数据文件中的 `prompts` 字段应该是包含用户提示的对象哈希。

对于每个条目，关键是变量名称和值 [Inquirer.js 问题对象](https://github.com/SBoudrias/Inquirer.js/#question)。

例：

```json
{
  "prompts": {
    "name": {
      "type": "string",
      "required": true,
      "message": "Project name"
    }
  }
}
```

所有提示完成后，`template` 内的所有文件将使用[Handlebars](http://handlebarsjs.com/) 进行渲染，并将提示结果作为数据。

### Prompts 条件控制

可以通过添加 `when` 字段来提示条件，该字段应该是使用从先前提示收集的数据评估的 JavaScript 表达式。

例如：

```json
{
  "prompts": {
    "lint": {
      "type": "confirm",
      "message": "Use a linter?"
    },
    "lintConfig": {
      "when": "lint",
      "type": "list",
      "message": "Pick a lint config",
      "choices": ["standard", "airbnb", "none"]
    }
  }
}
```

`lintConfig` 的提示只有在用户对 `lint` 提示符回答 yes 时才会触发。

### 我们预先加入了 `Handlebars` 助手

两个过滤器可以使用, `if_eq` 和 `unless_eq`:

```handlebars
{{#if_eq lintConfig "airbnb"}};{{/if_eq}}
```

### 自定义 `Handlebars` 助手

您可能想要在元数据文件中使用 `helpers` 属性注册更多的 `Handlebars` 助手。 对象键是助手名称：

```js
module.exports = {
  helpers: {
    lowercase: str => str.toLowerCase()
  }
}
```

注册后，他们可以使用如下：

```handlebars
{{ lowercase name }}
```

### 文件 filters

元数据文件中的 `filters` 字段应该是包含文件过滤规则的对象散列。

对于每个条目，关键是 [minimatch glob 模式](https://github.com/isaacs/minimatch)，该值是在提示答案数据的上下文中评估的 JavaScript 表达式。

例：

```json
{
  "filters": {
    "test/**/*": "needTests"
  }
}
```

`test` 下的文件只有在用户回答 `needTests` 的提示时才会生成。

请注意，minimatch的 `dot` 选项设置为 `true` ，因此 `glob模式` 默认情况下也会匹配 dotfiles。

### 跳过渲染文件

元数据文件中的 `skipInterpolation` 字段应该是[minimatch glob模式](https://github.com/isaacs/minimatch)。

匹配的文件应该跳过渲染。

例：

```json
{
  "skipInterpolation": "src/**/*.vue"
}
```

> `src` 目录下的所有 `.vue` 文件将跳过模板渲染

### Metalsmith

`itheima-cli` 使用[metalsmith](https://github.com/segmentio/metalsmith) 来生成项目。

您可以自定义由 `itheima-cli` 创建的metalsmith构建器来注册自定义插件。

```js
{
  "metalsmith": function (metalsmith, opts, helpers) {
    function customMetalsmithPlugin (files, metalsmith, done) {
      // Implement something really custom here.
      done(null, files)
    }

    metalsmith.use(customMetalsmithPlugin)
  }
}
```

如果您在提出问题之前需要挂钩 `metalsmith`，则可以使用 `before` 键上的对象。

```js
{
  "metalsmith": {
    before: function (metalsmith, opts, helpers) {},
    after: function (metalsmith, opts, helpers) {}
  }
}
```

> 注意 `metalsmith` `opts` `helpers` 不要忘记传入

### 保留变量 meta.{js,json}

* `destDirName` - 目标目录名

```json
{
  "completeMessage":
    "To get started:\n\n  cd {{destDirName}}\n  npm install\n  npm run dev"
}
```

* `inPlace` - 生成模板到当前目录

```json
{
  "completeMessage":
    "{{#inPlace}}To get started:\n\n  npm install\n  npm run dev.{{else}}To get started:\n\n  cd {{destDirName}}\n  npm install\n  npm run dev.{{/inPlace}}"
}
```

### `complete` 函数

参数:

* `data`: 您可以访问的数据相同 `completeMessage`:

```js
{
  complete (data) {
    if (!data.inPlace) {
      console.log(`cd ${data.destDirName}`)
    }
  }
}
```

* `helpers`: 一些助手可以用来记录结果。
* `chalk`: `chalk` 模块，颜色标记输出
* `logger`: 自定义 `Log`
* `files`: 生成的文件数组

```js
{
  complete (data, {logger, chalk}) {
    if (!data.inPlace) {
      logger.log(`cd ${chalk.yellow(data.destDirName)}`)
    }
  }
}
```

### 安装特定的模板版本

`itheima-cli` 使用工具 [download-git-repo](https://github.com/flipxfx/download-git-repo) 下载使用的官方模板。

`download-git-repo` 工具允许您通过在井号（`＃`）后面提供所需的分支名称来指定特定存储库的特定分支。

特定官方模板所需的格式是：

```bash
itheima init '<template-name>#<branch-name>' <project-name>
```

示例:

安装 [`1.0` branch](https://github.com/itheima2017/itheimaAdmin-template/dev/1.0) of the webpack-simple vue template:

```bash
itheima init 'itheimaAdmin-template#dev' mynewproject
```

> 官方仓库 不用写用户名

### 一份完整的示例

模板根目录下 [meta.js](https://github.com/itheima2017/itheimaAdmin-template/blob/master/meta.js)

该 `meta.js` 功能有:

* [x] 系统自检
* [x] npm 配置
* [x] mysql 配置
* [x] 自动初始 mysql 数据
* [x] 自动初始 npm 包
* [x] 自动初始 maven 吧
* [x] 文件渲染过滤

为了便于查看程序结构，去掉了部分功能代码。

```js
const path = require('path')
const fs = require('fs')

const {
  systemCheck,
  printMessage
} = require('./utils')
const pkg = require('./package.json')

const templateVersion = pkg.version

const { addTestAnswers } = require('./scenarios')

module.exports = {
  metalsmith: {
    // When running tests for the template, this adds answers for the selected scenario
    before: function(metalsmith, opts, helpers) {
      const cwd = process.cwd()
      systemCheck(cwd)
      addTestAnswers(metalsmith, opts, helpers)
    }
  },
  helpers: {
    template_version() {
      return templateVersion
    }
  },
  prompts: {
    name: {
      when: 'isNotTest',
      type: 'string',
      required: true,
      message: '项目名称',
    },
    description: {
      when: 'isNotTest',
      type: 'string',
      required: false,
      message: '项目说明',
      default: '黑马admin管理后台',
    },
    author: {
      when: 'isNotTest',
      type: 'string',
      required: false,
      message: '作者',
      default: 'yourname <yourname@xxxx.xxx>',
    }
  },
  filters: {
  },
  skipInterpolation: [
    "vueSPA/*.js",
    "vueSPA/*.babelrc",
    "vueSPA/*.editorconfig",
    "vueSPA/*.eslintignore",
    "vueSPA/*.gitignore",
    "vueSPA/*.md",
    "vueSPA/src/**/*.vue",
    "vueSPA/src/**/*.js",
    "vueSPA/src/**/*.md",
    "javaSpringBoot2/*",
    "javaSpringBoot2/.mvn/**/*",
    "javaSpringBoot2/src/main/java/**/*",
    "*"
  ],
  complete: async function(data, { chalk }) {
    const green = chalk.green
    printMessage(data, chalk)
  },
}
```

## 模块添加命令

### 使用场景

新建业务模块，是所有业务系统都要遇到的，按规范一通配置和编写。

这种有规律的事情，可以用脚本替你执行。

### 命令格式

```bash
itheima moduleAdd example
```

执行后就能在你的项目下，新建模块了。

### 模板编写要求

已下以前端 `vue` `spa` 项目举例

* 目录格式

```bash
├── script                    脚本目录
│   └── module-add            模块添加
│       ├── index.js          添加脚本
│       └── template          模板目录
```

* `index.js` 参考

```js
module.exports = {
  prompts: {
    name: {
      type: 'string',
      required: true,
      message: '模块名称'
    },
    author: {
      type: 'string',
      required: false,
      message: '模块',
      default: 'yourname <yourname@xxxx.xxx>'
    },
    description: {
      type: 'string',
      required: false,
      message: '模块说明',
      default: ''
    }
  }
}
```

这里简单的配置模块名称、模块作者、模块说明

复杂配置同上 `itheima init` 操作

## 版权

[MIT](https://opensource.org/licenses/MIT) license.

@传智研究院-研发部

江苏传智播客教育科技股份有限公司 &nbsp;版权所有 Copyright 2006-2018, All Rights Reserved

# IT 黑马脚手架工具

## 安装

环境: [Node.js](https://nodejs.org/en/) (>=6.x, 8.x preferred), npm version 3+ and [Git](https://git-scm.com/).

```bash
$ npm install -g itheima-cli
```

> macos 需要 `sudo`

## 基础使用

```bash
$ itheima init <template-name> <project-name>
```

* `黑马Admin` 商用基础模板

```bash
$ itheima init itheimaAdmin-template my-AdminProject
```

## 命令格式

```bash
$ itheima init --help

Usage: itheima-init <template-name> [project-name]

  Options:

    -c, --clone  克隆git
    --offline    使用缓存模板
    -h, --help   output usage information
  Examples:

    # 创建一个 黑马Admin 项目
    $ itheima init itheimaAdmin-template my-project

    # 创建一个自定义模板的项目
    $ itheima init username/repo my-project
```

## 导入你自己的模板

```bash
$ itheima init username/repo my-project
```

> `username/repo` 请替换成你的 github 账号/仓库

## 版权

@传智研究院-研发部

江苏传智播客教育科技股份有限公司 &nbsp;版权所有 Copyright 2006-2018, All Rights Reserved
